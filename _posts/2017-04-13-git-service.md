---
layout:     post
title:      "搭建Git仓库方法"
date:       2017-04-13 20:27:20
author:     "xiaoh"
header-img: "img/post-bg-default.jpg"
tags:
    - 编程
    - Linux
    - Git
---

### [简介](#summary)

GitHub就是一个免费托管开源代码的远程仓库。但是对于某些视源代码如生命的商业公司来说，既不想公开源代码，又舍不得给GitHub交保护费，那就只能自己搭建一台Git服务器作为私有仓库使用。

搭建Git服务器需要准备一台运行Linux的机器，强烈推荐用Ubuntu或Debian，这样，通过几条简单的apt命令就可以完成安装。

假设你已经有`sudo`权限的用户账号，下面，正式开始安装。

---

##### [安装GIT](#install)

```shell
$ sudo apt-get install git
```

##### [创建用户](#adduser)

创建一个`git`用户,用来运行`git`服务

```shell
$ sudo adduser git
```

##### [创建证书登录](#authorized)

收集所有需要登录的用户的公钥，就是他们自己的`id_rsa.pub`文件，把所有公钥导入到`/home/git/.ssh/authorized_keys`文件里，一行一个。

##### [初始化GIT仓库](#init)

先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令:

```shell
$ sudo git init --bare sample.git
