# node.js抛出异常digital envelope routines::unsupported

## 1. 问题描述👻

​	很久没有打开的Vue项目需要改点东西，打开后使用命令`npm run dev`本地运行时却抛出异常：`error:0308010C:digital envelope routines::unsupported`，以下是异常原文：

```bash
Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
 INFO  Starting development server...
10% building 2/4 modules 2 active .\WebApp\node_modules\webpack\hot\dev-server.jsError: error:0308010C:digital envelope routines::unsupported
    at new Hash (node:internal/crypto/hash:68:19)
    at Object.createHash (node:crypto:138:10)
    at module.exports (.\WebApp\node_modules\webpack\lib\util\createHash.js:135:53)
    at NormalModule._initBuildHash (.\WebApp\node_modules\webpack\lib\NormalModule.js:417:16)
    at handleParseError (.\WebApp\node_modules\webpack\lib\NormalModule.js:471:10)
    at .\WebApp\node_modules\webpack\lib\NormalModule.js:503:5
    at .\WebApp\node_modules\webpack\lib\NormalModule.js:358:12
    at .\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:373:3
    at iterateNormalLoaders (.\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:214:10)
    at iterateNormalLoaders (.\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:221:10)
    at .\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:236:3
    at runSyncOrAsync (.\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:130:11)
    at iterateNormalLoaders (.\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:232:2)
    at Array.<anonymous> (.\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:205:4)
    at Storage.finished (.\WebApp\node_modules\enhanced-resolve\lib\CachedInputFileSystem.js:55:16)
    at .\WebApp\node_modules\enhanced-resolve\lib\CachedInputFileSystem.js:91:9
10% building 2/5 modules 3 active ...ode_modules\babel-loader\lib\index.js!.\WebApp\node_modules\eslint-loader\index.js??ref--13-0!.\WebApp\src\main.jsnode:internal/crypto/hash:68
  this[kHandle] = new _Hash(algorithm, xofLen);
                  ^

Error: error:0308010C:digital envelope routines::unsupported
    at new Hash (node:internal/crypto/hash:68:19)
    at Object.createHash (node:crypto:138:10)
    at module.exports (.\WebApp\node_modules\webpack\lib\util\createHash.js:135:53)
    at NormalModule._initBuildHash (.\WebApp\node_modules\webpack\lib\NormalModule.js:417:16)
    at handleParseError (.\WebApp\node_modules\webpack\lib\NormalModule.js:471:10)
    at .\WebApp\node_modules\webpack\lib\NormalModule.js:503:5
    at .\WebApp\node_modules\webpack\lib\NormalModule.js:358:12
    at .\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:373:3
    at iterateNormalLoaders (.\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:214:10)
    at Array.<anonymous> (.\WebApp\node_modules\loader-runner\lib\LoaderRunner.js:205:4)
    at Storage.finished (.\WebApp\node_modules\enhanced-resolve\lib\CachedInputFileSystem.js:55:16)
    at .\WebApp\node_modules\enhanced-resolve\lib\CachedInputFileSystem.js:91:9
    at .\WebApp\node_modules\graceful-fs\graceful-fs.js:123:16
    at FSReqCallback.readFileAfterClose [as oncomplete] (node:internal/fs/read/context:68:3) {
  opensslErrorStack: [ 'error:03000086:digital envelope routines::initialization error' ],
  library: 'digital envelope routines',
  reason: 'unsupported',
  code: 'ERR_OSSL_EVP_UNSUPPORTED'
}

Node.js v21.6.0
```



## 2. 探索👓

​	问了下GPT，原来是node.js 的版本问题。

​	因为 node.js V17版本中最近发布的OpenSSL3.0, 而OpenSSL3.0对允许算法和密钥大小增加了严格的限制，可能会对生态系统造成一些影响。故此以前的项目在升级 nodejs 版本后会报错。



## 3. 解决方案✔

### 方案一（推荐）⭐

​	修改package.json，在相关构建命令之前加入`SET NODE_OPTIONS=--openssl-legacy-provider`

```
"scripts": {
   "serve": "SET NODE_OPTIONS=--openssl-legacy-provider && vue-cli-service serve",
   "build": "SET NODE_OPTIONS=--openssl-legacy-provider && vue-cli-service build"
},
```

​	这种可以一劳永逸，以后直接通过npm执行scripts里面的命令即可。不管是项目迭代，还是团队开发，这种都比较有效。



## 方案二

​	当次运行的命令窗口有效：在你当前文件的cmd命令窗口输入：SET NODE_OPTIONS=--openssl-legacy-provider， 回车后输入 npm运行命令。



### 方案三

​	回退node.js版本。。。