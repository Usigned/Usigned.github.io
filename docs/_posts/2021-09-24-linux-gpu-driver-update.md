---
title: [linux显卡驱动更新]
tags: [linux]
---

最近想更新一下pytorch，结果更新到最新版之后提示nvidia显卡驱动太老了需要更新，现记录一下更新过程。

# 下载驱动

1. 首先去[nvidia官网](https://www.nvidia.com/Download/index.aspx)上找到对应的驱动，服务器上显卡是1080ti，故我选择如下配置

   ![image-20210924192529349](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20210924192529349.png)

2. 获取到下载链接后，在服务器上使用wget下载

   > wget https://us.download.nvidia.com/XFree86/Linux-x86_64/470.74/NVIDIA-Linux-x86_64-470.74.run

# 安装

1. 首先确保已经关闭所有使用显卡的进程

   - 使用`nvidia-smi`命令查看占用显卡的进程，并用`kill -9`杀掉

   ![image-20210924192941991](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20210924192941991.png)

2. 执行安装

   > sudo ./NVIDIA-Linux-x86_64-470.74.run

# Trouble shooting

1. 在安装过程中发现`Xorg`和`gnome-shell`进程杀死后会马上重启

   解决方法：[linux关闭 X server](https://www.zybuluo.com/upuil/note/922035)

2. [tensorflow - How to unload an NVIDIA kernel module 'nvidia' for new driver installation? - Stack Overflow](https://stackoverflow.com/questions/61543214/how-to-unload-an-nvidia-kernel-module-nvidia-for-new-driver-installation)

   

