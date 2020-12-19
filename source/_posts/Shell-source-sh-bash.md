---
title: Shell source sh bash ./
date: 2020-04-30 14:53:12
categories: 
- shell
tags:
- linux
- shell
---

# Shell source sh bash ./

## 打开子shell

### sh/bash

打开子shell执行目标脚本，sh相当于bash的POSIX语法的软连接，两者都无需执行权限

```shell
sh filename.sh
bash filename.sh
```

###  ./

打开子shell执行目标脚本，需要执行权限

```shell
./ filename.sh
```



## 不打开子shell

### source

在当前shell内执行目标脚本，无需执行权限

```shell
source filename.sh
```

### 

