# 一次真实的 Let's Encrypt 证书更新失败与修复全记录  
> **关键词：Certbot、Nginx、证书过期、ACME 验证失败、File exists、ssl_certificate 加载错误**

---

我的个人网站长期使用 **Let's Encrypt + Certbot + Nginx** 提供 HTTPS 服务，并配置了自动续期。某天突然发现浏览器提示 **“您的连接不是私密连接”** —— 证书居然过期了！

于是我立马登上服务器，查看证书状态：

```bash
sudo certbot certificates
```

输出显示：
```bash
Expiry Date: 2025-09-22 ... (INVALID: EXPIRED)
```

而今天已经是 **2025年11月底**。虽然早已配置 `systemd` 定时任务自动续期，但显然它**没有生效**。



## 一、初步排查

#### 1. 手动续期报错：`File exists`

在检查了定时任务状态正常后，我尝试通过手动续期的方式来看看续期过程中是否出现异常，果不其然：

```bash
sudo certbot renew
```

报错：
```
[Errno 17] File exists: '/etc/letsencrypt/archive/www.version-carol.cn/privkey2.pem'
```

这很反常——Certbot 一般会自动处理文件命名，怎么会“文件已存在”？

我猜测是之前某次续期中断，导致部分文件残留，Certbot 试图写入相同编号的文件时被拒绝。

#### 2. 清理后“看似成功”，实则证书未更新
按常规操作，我备份并移除了 `privkey2.pem` 等冲突文件，再次运行 `renew`，终端提示“成功”。

但检查证书有效期却发现：
```bash
openssl s_client -connect www.version-carol.cn:443 | openssl x509 -noout -dates
# notAfter=Sep 22 05:39:38 2025 GMT ← 仍然是旧证书！
```

Certbot 并未真正签发新证书，只是正常走了一遍流程。



## 二、根本原因：Nginx 配置与 Certbot 插件的耦合陷阱

我决定彻底重来，从头生成新的证书，执行：

```bash
sudo certbot delete --cert-name www.version-carol.cn
sudo certbot certonly --nginx -d www.version-carol.cn
```

结果报错：

```
nginx: [emerg] BIO_new_file("/.../fullchain.pem") failed (No such file)
```

这下子我有点明白了：

- Nginx 配置中仍保留着 `ssl_certificate /.../fullchain.pem;`
- 删除证书后，该文件被移除，但 Nginx 配置未变；
- Certbot 的 `--nginx` 插件在申请前会执行 `nginx -t` 测试配置；
- Nginx 启动失败 → Certbot 中断 → 无法申请新证书。

这不是死循环了吗...

要申请证书，必须通过 `nginx -t`；  但 `nginx -t` 成功，又依赖证书文件存在。



## 三、解决方案：绕过 Nginx 插件，使用 `--webroot` 模式

为了避免插件依赖，我改用 **HTTP 文件验证（webroot）模式**，步骤如下：

#### 步骤 1：修改 Nginx HTTP 配置，放行 ACME 路径

原配置：
```nginx
server {
    listen 80;
    server_name www.version-carol.cn;
    location / {
        return 301 https://$host$request_uri;  # 所有请求都被跳转，包括验证！
    }
}
```

**问题**：Let's Encrypt 无法访问 `http://www.version-carol.cn/.well-known/acme-challenge/xxx`，验证失败。

**修复**：

```nginx
server {
    listen 80;
    server_name www.version-carol.cn;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
        try_files $uri =404;
    }

    location / {
        return 301 https://$host$request_uri;
    }
}
```

#### 步骤 2：临时注释 HTTPS 块中的 `ssl_certificate` 行

在 `/etc/nginx/nginx.conf` 中：
```nginx
server {
    listen 443 ssl;
    server_name www.version-carol.cn;
    # ssl_certificate /etc/letsencrypt/.../fullchain.pem;   ← 临时注释
    # ssl_certificate_key /etc/letsencrypt/.../privkey.pem; ← 临时注释
    ...
}
```

> 这样 `nginx -t` 才能通过，Certbot 才有机会运行。

#### 步骤 3：使用 `--webroot` 申请证书

```bash
sudo mkdir -p /var/www/certbot
sudo certbot certonly --webroot -w /var/www/certbot -d www.version-carol.cn
```

成功！新证书生成于 `/etc/letsencrypt/live/www.version-carol.cn/`

#### 步骤 4：恢复配置并重载 Nginx

- 取消注释 `ssl_certificate` 行；
- 重载服务：
  ```bash
  sudo nginx -t && sudo systemctl reload nginx
  ```

##### 步骤 5：验证

```bash
echo | openssl s_client -connect www.version-carol.cn:443 2>/dev/null | openssl x509 -noout -dates
# notAfter=Feb 25 10:00:00 2026 GMT ← 成功！
```



## 四、思考：为什么 `--nginx` 插件在此场景下会失败？

| 模式        | 优点                              | 缺点                        | 适用场景                         |
| ----------- | --------------------------------- | --------------------------- | -------------------------------- |
| `--nginx`   | 自动修改 Nginx 配置、无需手动干预 | 依赖 Nginx 配置完整且可启动 | 初次部署、配置干净               |
| `--webroot` | 不依赖 Nginx 启动，只读写指定目录 | 需手动配置 ACME 路径        | 证书已删除、配置复杂、高可用环境 |

在**证书已删除但 Nginx 配置未清理**的场景下，`--nginx` 插件会因 `nginx -t` 失败而退出，形成死循环。而 `--webroot` 完全绕过此问题。



## 五、 预防措施

1. **所有 HTTP server 块必须放行 ACME 路径**：
   
   ```nginx
   location /.well-known/acme-challenge/ {
       root /var/www/certbot;
       try_files $uri =404;
   }
   ```
   
2. **不要手动删除 `/etc/letsencrypt/live/` 下的文件**，务必用 `certbot delete`。

3. **自动续期测试**：
   
   ```bash
   sudo certbot renew --dry-run
   ```
   建议每月手动执行一次。
   
4. **监控证书有效期**：可通过脚本 + 邮件/Cron 告警，或使用 [UptimeRobot](https://uptimerobot.com/) 等服务监控 SSL 过期。





#### 六、总结

- **自动续期 ≠ 一劳永逸**，需定期验证；
- **Nginx 配置与 Certbot 紧密耦合**，修改时需谨慎；
- **`--webroot` 是处理“烂摊子”的终极武器**；
- **ACME 验证路径必须可访问**，否则一切免谈。

这次故障虽然折腾，但也让我彻底理清了 Certbot 与 Nginx 的交互逻辑。希望这篇记录能帮到同样踩坑的你。

> **技术没有银弹，但有经验可复用。**
