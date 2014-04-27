---
layout: post
title: "如何修复/home分区和swap分区被误删的Ubuntu 12.04"
date: 2012-07-01 21:00
comments: true
categories: linux ubuntu
---
同事在Ubuntu上调试程序时，眼花缭乱中将挂载到**/home**的分区和**SWAP分区**给删除了，导致她的系统无法登录。由于系统上已经安装了很多系统更新和开发工具，新装一个系统要重复安装很多软件包。这些软件都安装在根目录所在的分区，所以依然在系统中，如果可以修复就避免了安装系统更新和开发工具要耗费的大量时间。基本上有两件事要解决：

1. 重新做一个挂载**/home**的分区出来（目前挂载根目录的分区只有20GB，不够用）；
2. 恢复用户主目录的基本目录结构，包括一些默认的用户配置。

首先重启系统，用GParted Live CD（或USB Stick）启动，用GPartd分区工具重新建两个分区出来：一个格式化为Ext4文件系统；一个不要格式化，标记为swap分区。记住这两个分区对应的设备节点。

然后重新启动。由于Ubuntu 12.04并不支持在启动时修改GRUB的内核启动参数，无法直接进入单用户模式操作；只能进入Ubuntu的恢复模式，选择“Drop to root shell”。进入root shell后，注意到整个文件系统都只读。这时要对配置文件做一些修改的话，必须将文件系统重新挂在为可读写的：

``` bash
mount -n -o remount,rw /
```

之后修改**/etc/fstab**文件。现在这个文件都使用uuid来标识分区，但依然支持过去的用法——直接使用设备节点。删除掉以前的/home和swap对应的uuid条目，替换成新的格式化后分区的设备节点，比如/dev/sda5，/dev/sda6。这样就解决了/home的挂载问题。

下面再解决另外一个问题：用户的主目录为空。由于过去的主目录内容已经全部丢失，而且找回的几率很小，那就可以先删除用户再添加用户重新根据/etc/skel的内容生成用户主目录中的内容。简单的说如下：

``` bash
userdel -r $FAKE_USER
useradd -n -m -k -u uid  $FAKE_USER
passwd $FAKE_USER
```

如果你担心以前用户写入的文件无法读取，你可以在重新添加用户时指定用户UID。这样子就可以拥有以前用户UID所拥有的权限了。

如果你还遇到新生成的$FAKE_USER登录后无法通过sudo提权的问题，可以重新回到恢复模式下，依然将文件系统remount为可读写的，直接修改**/etc/sudoers**文件：

``` bash
root  ALL=(ALL:ALL) ALL
$FAKE_USER ALL=(ALL:ALL) ALL
```

或将$FAKE_USER加入到admin组中：

``` bash
usermod -a -G admin $FAKE_USER
```

重新启动，系统重新恢复正常。
