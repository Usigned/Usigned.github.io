---
title: SteamDeck 上安装暗潮mod
tags: [other]
---

Steam Deck(SD) 上安装暗潮mod

## 前置步骤

1. 下载游戏本体
   - 在sd上直接通过商店下载安装即可
2. 下载mod文件
3. 切换至桌面模式将mod拷贝到暗潮的安装目录

其中2、3步骤可以参考b站其他up在windows上安装暗潮mod的教程

链接：https://www.bilibili.com/read/cv22076663

## mod激活

win系统上在将mod文件拷贝至暗潮安装目录后，会需要你执行一个名为`toggle_darktide_mods.bat`的批处理文件。该文件主要作用是将mod中的二进制文件集成到游戏本体中来激活mod。但是该批处理文件采用了windows 系统中的语法，所以无法在steamos(linux) 上使用。

![image-20231024090944130](https://cdn.jsdelivr.net/gh/Usigned/pic-typora@main/images/image-20231024090944130.png)

而且在查看bat文件内容的时候可以看到，该bat文件调用了一个`.exe`文件。`.exe`文件是win系统上的二进制文件无法直接在linux上运行，因此我们需要一个方法在linux上运行`.exe`文件，之后手动执行命令来激活mod。

![image-20231024091221657](https://cdn.jsdelivr.net/gh/Usigned/pic-typora@main/images/image-20231024091221657.png)

### 安装wine兼容层

我采取的方式是安装wine这个兼容层来运行`dtkit-patch.exe`这个二进制工具程序，步骤如下，首先按以下步骤进入命令行终端：

1. sd切换至桌面模式
2. 打开暗潮安装目录
3. 右键open terminal打开命令行终端

在命令行中依次执行：

1.  设置`deck`用户的密码： `passwd`
2. 关闭steamos的只读模式：`sudo btrfs property set -ts / ro false`

3. 设置`pacman`（我理解是一个包管理工具）
   1. `sudo pacman-key --init`
   2. `sudo pacman-key -- populate archlinux`

> 这部分可以参考[Install Arch packages on your Steam Deck : SteamDeck (reddit.com)](https://www.reddit.com/r/SteamDeck/comments/t8al0i/install_arch_packages_on_your_steam_deck/)

在命令行中使用`pacman`安`装wine`

```shell
> sudo pacman -S wine
```

> 安装中可能会报如下错误
>
> error: lib32-libpcap: signature from "David Runge <dvzrv@archlinux.org>" is unknown trust
> :: File /var/cache/pacman/pkg/lib32-libpcap-1.10.1-1-x86_64.pkg.tar.zst is corrupted (invalid or corrupted package (PGP signature)).
>
> 这是由于无法校验包签名导致的，我采取的解决方法是按照描述[pacman/Package signing - ArchWiki (archlinux.org)](https://wiki.archlinux.org/title/Pacman/Package_signing#Disabling_signature_checking)**关闭包的签名校验**。
>
> 1. 打开`/etc/pacman.conf`文件
>
> 2. 在`[options]`下修改配置为如下
>
>    ```
>    SigLevel = Never
>    #LocalFileSigLevel = Optional
>    #RemoteFileSigLevel = Required
>    ```

安装完毕后，执行以下命令校验一下是否安装成功，未提示找不到`wine`命令则表示安装成功。

```shell
> which wine
```

### mod激活

1. 找到暗潮的安装目录，也就是bat文件所在的目录（注意此处路径不能错）

2. 右键打开命令行

3. 执行命令

   ```shell
   > wine tools/dtkit-patch --toggle ./bundle
   ```

4. 执行过程中会弹出一堆错误提示，但是最后会显示`successful patched`表示安装完成

再启动游戏，就可以发现mod已经打上了～～