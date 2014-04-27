---
layout: post
title: "如何给一些Mac命令行工具设置代理服务器"
date: 2013-04-20 21:00
comments: true
categories: thoughts
---

在公司环境里，有些工具在防火墙后面无法直接使用，必须通过HTTP/HTTPS代理服务器才能访问外网的数据。通常，在命令行环境中设置好HTTP\_PROXY和HTTPS\_PROXY环境变量就能使用（如curl）：

``` bash
➜  ~  export HTTP_PROXY=http://192.168.80.100:3128/
➜  ~  export HTTPS_PROXY=https://192.168.80.100:3128/
```

但下面这些工具需要特殊设置：

- homebrew：设置了HTTP\_PROXY也不行，还是不能用。需要设置http\_proxy才行。
``` bash
➜  ~  brew install git
==> Downloading http://git-core.googlecode.com/files/git-1.8.2.1.tar.gz

curl: (7) Failed to connect to 2404:6800:4008:c00::52: No route to host
Error: Download failed: http://git-core.googlecode.com/files/git-1.8.2.1.tar.gz
➜  ~  export http_proxy=http://192.168.80.100:3128/
➜  ~  export https_proxy=https://192.168.80.100:3128/
➜  ~  brew install git
==> Downloading http://git-core.googlecode.com/files/git-1.8.2.1.tar.gz
######################################################################## 100.0%
==> make prefix=/usr/local/Cellar/git/1.8.2.1 CC=cc CFLAGS= LDFLAGS= install
==> make CC=cc CFLAGS= LDFLAGS=
==> make clean
==> make CC=cc CFLAGS= LDFLAGS=
==> Downloading http://git-core.googlecode.com/files/git-manpages-1.8.2.1.tar.gz
######################################################################## 100.0%
==> Downloading http://git-core.googlecode.com/files/git-htmldocs-1.8.2.1.tar.gz
######################################################################## 100.0%
==> Caveats
The OS X keychain credential helper has been installed to:
  /usr/local/bin/git-credential-osxkeychain

The 'contrib' directory has been installed to:
  /usr/local/share/git-core/contrib

Bash completion has been installed to:
  /usr/local/etc/bash_completion.d

zsh completion has been installed to:
  /usr/local/share/zsh/site-functions
==> Summary
🍺  /usr/local/Cellar/git/1.8.2.1: 1306 files, 27M, built in 47 seconds
```

- subversion：在防火墙后面访问不了外面的HTTP repo。需要修改*~/.subversion/servers*（如果文件不存在需要自行添加）：
``` bash
[groups]
group1 = *.9pm.me

[group1]
http-proxy-host = 192.168.80.100
http-proxy-port = 3128

```
