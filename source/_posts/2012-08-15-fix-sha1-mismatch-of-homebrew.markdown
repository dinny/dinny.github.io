---
layout: post
title: "修复通过homebrew安装工具时出现的SHA1 Mismatch问题"
date: 2012-08-15 21:00
comments: true
categories: homebrew commandline
---

要给社区的某个工具做个Mac build，而这个工具依赖pyqt，想通过[homebrew](http://mxcl.github.com/homebrew/)来安装。运行了一下`brew install pyqt`，结果输出“SHA1 mismatch”，报错：

``` bash
dinny-macbook-air:linkchecker dinny$ brew install pyqt
==> Installing pyqt dependency: qt
==> Downloading https://downloads.sf.net/project/machomebrew/Bottles/qt-4.8.0-bottle.tar.gz
File already downloaded in /Users/dinny/Library/Caches/Homebrew
Error: SHA1 mismatch
Expected: d03b56811d2cac933b6103bd4c8ac636dea3b877
Got: 2bfe00c5112b0d2a680cd01144701f8937846096
Archive: /Users/dinny/Library/Caches/Homebrew/qt-4.8.0.bottle.tar.gz
(To retry an incomplete download, remove the file above.)
dinny-macbook-air:linkchecker dinny$ rm /Users/dinny/Library/Caches/Homebrew/qt-4.8.0.bottle.tar.gz
dinny-macbook-air:linkchecker dinny$ brew install pyqt
==> Installing pyqt dependency: qt
==> Downloading https://downloads.sf.net/project/machomebrew/Bottles/qt-4.8.0-bottle.tar.gz
######################################################################## 100.0%
Error: SHA1 mismatch
Expected: d03b56811d2cac933b6103bd4c8ac636dea3b877
Got: 2bfe00c5112b0d2a680cd01144701f8937846096
Archive: /Users/dinny/Library/Caches/Homebrew/qt-4.8.0.bottle.tar.gz
(To retry an incomplete download, remove the file above.)
```

这个问题更新了brew就没问题了：

``` bash
dinny-macbook-air:linkchecker dinny$ brew update
dinny-macbook-air:linkchecker dinny$ brew install pyqt
==> Installing pyqt dependency: qt
==> Downloading https://downloads.sf.net/project/machomebrew/Bottles/qt-4.8.0-bottle.tar.gz
File already downloaded in /Users/dinny/Library/Caches/Homebrew
==> Pouring qt-4.8.0.bottle.tar.gz
==> Caveats
We agreed to the Qt opensource license for you.
If this is unacceptable you should uninstall.
==> Summary
/usr/local/Cellar/qt/4.8.0: 2722 files, 193M
==> Installing pyqt
```
