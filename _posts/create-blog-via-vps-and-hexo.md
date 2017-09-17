---
title: 在VPS搭载HEXO博客并使用git部署
date: 2017-08-12 12:01:02
tags: hexo
---

# 在VPS搭载HEXO博客并使用git部署

## 环境说明

* 本地PC使用archlinux
* VPS为CentOS

## 静态博客

**静态博客**指的是使用静态页面的博客，这些网页都是提前生成好的，不需要数据库，并且内容不会动态改变，与 WordPress 等动态博客相比，静态博客具有以下优点：

1. 加载速度快，这是静态页面的优势，然而服务器网络访问差可能会让这一点变得毫无意义
2. 占用资源少，只需要一个 Web 服务器引擎即可
3. 易于维护、迁移，基本上你要操心的只有文本格式的源文件

当然静态博客也有一些缺点，比如对新手不友好、每次修改配置都需要重新生成博客、文章数量多的时候博客生成速度慢等。

## 原理说明

在 VPS 上搭建 Hexo 其实和在 GitHub 上面没有多大区别，同样是在本地编辑文本，然后使用 Git 远程部署到 Git 仓库。只不过在 VPS 上面需要配置 Git Hooks 将博客文件自动 push 到 Web 根目录。

## 本地配置

### 安装 Git 和 Node.js

HEXO使用Node.js编写，所以我们需要先下载Node

```shell
sudo pacman -S git nodejs
```

### 配置git

```shell
git config --global user.name "example"
git config --global user.email "example@ex.com"
```

由于 Hexo 的 Git 部署不支持使用密码登陆，所以需要配置 SSH 公钥：

```shell
cd ~
ssh-keygen -t rsa
cd .ssh
```

在系统当前用户文件夹下生成了私钥 `id_rsa` 和公钥 `id_rsa.pub` ，其中公钥需要上传到服务器或者在服务器新建公钥文件并粘贴公钥内容，待会会用到

### 建立Hexo仓库

```shell
mkdir hexo && cd hexo
npm install -g hexo-cli
hexo init
```

这样就生成了一个简单的博客，可以输入

```shell
hexo server
```

来启动本地服务器，使用

```shell
hexo new post "Hello World"
```

即可新建一篇文章，用文本编辑器打开 `hexo/source/_post/Hello World.md` 可以快速开始写作。

## 服务器配置

### 安装Git和Nginx

```shell
yum install git nginx
```

### 添加git用户并添加权限

新建git用户

```shell
addusr git
```

然后给为其添加`sudo`权限

```shell
vim /etc/sudoers
```

在文件中加入下面这一行

```
git ALL=(ALL) ALL
```

### 配置git

添加ssh公钥

```shell
scp ~/.ssh/ip_rsa.pub git@your-vps-ip:~/.ssh/authorized_keys
```

建立git仓库

```shell
mkdir hexo.git
cd hexo.git
git init --bare
```

创建网站目录

```shell
cd /var/www
mkdir hexo
chown git:git -R /var/www/hexo
```

### 配置 Git Hooks

```shell
cd ~/hexo.git/hooks
```

编辑`post-receive`文件：

```shell
#!/bin/bash
GIT_REPO=/home/git/hexo.git #git 仓库
TMP_GIT_CLONE=/tmp/hexo
PUBLIC_WWW=/var/www/hexo #网站目录
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
```

赋予可执行权限：

```shell
chmod +x post-receive
```

### 配置Nginx

### 配置 Nginx

```shell
vi /etc/nginx/conf.d/hexo.conf
```

如下图所示

```
server {
  listen         80 ;
  root /var/www/hexo;
  server_name blog.lecoan.me;  	//根据实际情况修改
  access_log  /var/log/nginx/hexo_access.log;
  error_log   /var/log/nginx/hexo_error.log;
  location ~* ^.+\.(ico|gif|jpg|jpeg|png)$ {
    root /var/www/hexo;
    access_log   off;
    expires      1d;
  }
  location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
    root /var/www/hexo;
    access_log   off;
    expires      10m;
  }
  location / {
    root /var/www/hexo;
    if (-f $request_filename) {
    rewrite ^/(.*)$  /$1 break;
    }
  }
}
```

然后重启Nginx并设置开机启动

```shell
systemctl restart nginx.service
systemctl enable nginx.service
```

## 发布文章

在本地编辑好文章之后使用 `hexo g` 生成静态网页，`hexo s` 在本地预览，`hexo d` 提交到服务器