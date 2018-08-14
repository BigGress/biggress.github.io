---
title: fis3基础命令
date: 2016-01-14 20:03:04
tags:
---
# fis3基础教程

### fis3的安装

``` bash
//对于没有翻墙的朋友可以去百度（淘宝npm），对于有翻墙的朋友直接使用npm
npm install fis3 -g
```

### fis3基础概念及命令

 * 1、发布
``` bash
//默认发布
fis3 release
//在指定路径输入文件
fis3 release -d <path>

//其他指令
-w 监听文件
-L 浏览器自动刷新
-f fis的配置文件路径（fis-conf.js）
--no-color 发布时编译没有颜色
--verbose 编译时输出详细
//除了以上几点外，本人也有一些没搞清楚的
-l,-c,-u,-r,-f
```
 * 2、服务
``` bash
//启动fis服务

fis3 server start

//停止fis服务

fis3 server stop

//重启fis服务

fis3 server restart

//打开fis路径

fis3 server open

//删除fis路径下的所有文件

fis3 server clean

//查看fis的配置信息

fis3 server info

//在指定端口打开

fis3 server start -p 端口

//在指定路径下启动fis

fis3 server start --www 路径

//启动https

fis3 server start --https

//除了以上的常用指令外fis3还有额外的指令
--timeout 启动超时时间
--no-browse 启动时不打开浏览器
--no-daemon 启动后监听请求
--type 启动模板类型（php,smarty,laveral）官方就这三种
--root 设置fis的默认根目录(以后用fis3 server start 都会从这个路径启动)
```