---
title: git-commit之Message指南
date: 2017-04-17 00:24:41
tags:
---

由于commit的message可以随便写，所以利用commitizen 规范化message内容。

### 安装

``` bash
    npm install -g commitizen

    commitizen init cz-conventional-changelog --save --save-exact
```

### Message的格式

``` 
    // Header
    <type(<scope): <subject
    // body
    <body
    // footer
    <footer
```

1、 Type类型

 ```
    feat: 新功能
    fix: 修补bug
    docs: 文档
    style: 格式 (不影响代码运行)
    refactor: 重构
    test: 增加测试
    chore: 构建过程或者辅助工具变动
    revert: 撤销之前的commit
 ```

2、 Scope
 
 用于说明本次commit所影响的范围（ng中可以是component、module、filter等）

3、 Subject

 一个以动词开头的一句简短的话，用以描述本次Commit的内容

4、 Body

 本次Commit的具体描述

5、 Footer 

 有两种情况：

5.1、不兼容变动

 如果当前代码与上一个版本不兼容，则 Footer 部分以BREAKING CHANGE开头，后面是对变动的描述、以及变动理由和迁移方法。

5.2、关闭Issue

 本次commit针对某个issue。