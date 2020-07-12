---
title: mkcert和browser-sync搭建https
date: 2020-07-12 23:32:24
tags:
---

随着https的普及，未来越来越多的应用会使用https，浏览器也越来越多让一些的功能只能在https下运行(摄像头、http的网络请求等)。所以为了开发、测试时候能模拟最真实的环境，我们也应当学会如何使用https。

今天就用`mkcert`，给大家介绍下如何创建一个本地受信任的ssl证书。`mkcert`几乎屏蔽所有的证书的创建流程，只需输入需要创建的domain。

# 安装

> 通过brew来安装mkcert

MacOS

``` ssh
brew install mkcert
```

> 本地信任mkcert

```ssh
mkcert -install
```

> 创建证书

我们创建一个同时支持localhost和www.haha.com的证书

```ssh
mkcert localhost www.haha.com
```

运行这个命令你就会得到两份文件`localhost+1.pem`和`localhost+1-key.pem`两份文件，接下来只需要在https的服务中添加这两个证书即可。

# 创建https服务

下面分别原生https和browser-sync来创建https服务器

> 原生https

创建`https-server.js`

```javascript
const https = require("https");
// const https = require("http");
const fs = require("fs");

const options = {
  key: fs.readFileSync("localhost+1-key.pem"),
  cert: fs.readFileSync("localhost+1.pem")
};

https
  .createServer(options, (req, res) => {
    console.log(req);
    res.writeHead(200);
    res.end("hello world\n");
  })
  .listen(8000);
```

最后运行`node ./https-server.js`

> browser-sync

使用browser-sync了话，我们需要在目录下创建一个index.html文件，内容如下

``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  hello world
</body>
</html>
```

创建`start-browser.js`


```javascript
const browserSync = require('browser-sync');

const path = require('path')

browserSync({
  server: "./",
  host: 'qwer.com',
  port: 8000,
  https: {
      key: path.resolve(__dirname, "./localhost+1-key.pem"),
      cert: path.resolve(__dirname, "./localhost+1.pem")
  }
});
```

最后运行`node ./start-browser.js`

来我们分别访问下`https://localhost:8080`和`https://qwer.com:8080`。得到的结果如下：

![](https://pic2.zhimg.com/v2-212a01daea6bdb106d4f991ebe4b03c9_b.png)
![](https://pic2.zhimg.com/v2-6755ca1b517ed36db5f9171020335fc9_b.png)


自此https服务器创建完成