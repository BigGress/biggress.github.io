---
title: ionic报错
date: 2016-01-25 22:54:43
tags:
---

# chrome远程调试报错

* 1、No Content-Security-Policy meta tag found. Please add one when using the cordova-plugin-whitelist plugin.
  * 在控制台看到这句话...说明cordova-plugin-whitelist需要一个白名单...
  在index.html下加入
``` bash

    <meta http-equiv="Content-Security-Policy"/>

```
  在config.xml里加入
``` bash
<allow-navigation href="http://example.com/*"/>
<content src="http://www.example.com/"/>
1
```

