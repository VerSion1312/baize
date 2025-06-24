# 自己创建SSL证书并自动续期

网站之前的ssl证书全部都通过某厂云服务来免费创建的，每次有效期只有三个月，到期以后要自行登录平台去申请、下载、替换，非常麻烦，而且每年还有次数限制。

在一次学习网络通信证书相关知识时，无意间了解到了**Certbot** 可以为免费申请 **Let's Encrypt** 的受信任证书，可以适用于生产环境，且浏览器不会提示不安全。经过一番尝试，现已完全搞定！网站现在ssl证书有效，且每三个月到期时会自动续期，非常滴方便！特此进行记录，以便后续使用。（基于 **CentOS + Nginx** 环境）



### **1. 安装 Certbot 和插件**

```bash
# 安装 EPEL 仓库（如果未安装）
sudo yum install epel-release -y

# 安装 Certbot 和 Nginx 插件
sudo yum install certbot python3-certbot-nginx -y
```

------



### **2. 确保 Nginx 配置正确**

- 我的域名 `www.version-carol.cn` 已经通过我的服务器供应商添加了DNS解析，使域名能够解析到服务器IP（必要）。

- 网站已通过Nginx进行配置发布。

- Nginx 中需有对应的`server`配置块，且`server_name`包含该域名。

  示例：`/etc/nginx/nginx.conf`：

  ```nginx
  server {
      listen 80;
      server_name www.version-carol.cn;
      root /var/www/html;
      # 其他配置...
  }
  ```

- 检查配置并重启 Nginx：

  ```bash
  sudo nginx -t   # 测试配置
  sudo systemctl restart nginx
  ```

------



### **3. 使用 Certbot 申请证书**

```bash
sudo certbot --nginx -d www.version-carol.cn # 自动为域名申请证书并修改 Nginx 配置
```

- 如果希望同时包含其他域名，可在命令后使用`-d`追加。（如现在需要追加 `chat.version-carol.cn`，便在上述命令后添加 `-d chat.version-carol.cn`）。
- 首次运行会提示输入邮箱（用于紧急通知），并同意服务条款。

------



### **4. 验证证书自动续订**

Let's Encrypt 证书有效期为 **90 天**，Certbot 会自动配置定时任务续订：

```bash
sudo certbot renew --dry-run # 验证续订是否生效
```

- 如果输出 **Congratulations** 表示自动续订配置成功。

------



### **5. 确认证书生效**

- 访问 `https://www.version-carol.cn`，浏览器应显示绿色锁标志。

- 检查证书信息：

  ```bash
  sudo certbot certificates
  ```

  输出会显示证书路径、有效期等。

  ```bash
  Saving debug log to /var/log/letsencrypt/letsencrypt.log
  
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Found the following certs:
    Certificate Name: www.version-carol.cn
      Serial Number: 
      Key Type: RSA
      Domains: www.version-carol.cn
      Expiry Date: 2025-09-22 05:39:38+00:00 (VALID: 89 days)
      Certificate Path: /....../fullchain.pem
      Private Key Path: /....../privkey.pem
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  ```

  

------



### **6. 其他注意事项**

1. #### 防火墙需要开放 443 端口

   ```
   sudo firewall-cmd --add-service=https --permanent
   sudo firewall-cmd --reload
   ```

2. #### 可选强制 HTTPS 跳转

   Certbot 通常会自动修改 Nginx 配置，将 HTTP 重定向到 HTTPS。如果没有，可手动在 Nginx 配置中添加：

   ```
   server {
       listen 80;
       server_name www.version-carol.cn;
       return 301 https://$host$request_uri;
   }
   ```