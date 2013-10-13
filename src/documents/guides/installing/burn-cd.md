# 刻录 Haiku 光盘

为了制作 Haiku 安装光盘，您需要将相应的 ISO 或者 Anyboot 镜像刻录到光盘。有许多不同的方法都可以完成刻录，下面是不同操作系统下的刻录指南和刻录软件列表：

## 刻录Anyboot镜像警告

某些光盘刻录软件可能会显得“智能化”，但却无法正确的刻录 Anyboot 镜像。而 Burn 2.4.1.u，CDRecord，Infrarecorder，以及 K3b 据称可以很好的刻录 Anyboot 镜像，甚至不需要将文件名改为 *.iso。

## Ubuntu Linux

* 如何刻录 ISO 镜像 (适用于 Ubuntu、Kubuntu、Xubuntu)

## FreeBSD

* FreeBSD操作手册章节 18.6.3

## Mac OS X

* SimplyBurns: http://simplyburns.berlios.de

## Windows

* InfraRecorder (开源软件): http://infrarecorder.org
* ImgBurn (自由软件): http://www.imgburn.com
* ISO Recorder (自由软件): http://isorecorder.alexfeinman.com/isorecorder.htm
* Active@ ISO Burner (自由软件): http://www.ntfs.com/iso_burner_free.htm

## 在终端中使用 cdrecord (Linux/UNIX，BeOS等的主要风格)

在大部分的 Linux/UNIX，BSD 以及 BeOS 中，您可以从终端中使用 cdrecord 命令将 ISO 或者 Anyboot 镜像文件刻录到光盘。

`cdrecord dev=x,y,z driveropts=burnfree -v -eject -dao -data haiku.iso`

x,y,z 是使用 cdrecord -scanbus 命令检查出的硬件编号。(在 Linux 下，它也可以是一个硬件的路径)，而 haiku.iso 是用于刻录到光盘的 ISO 或者 Anyboot 镜像。需要提醒的是，您不需要对 Anyboot 镜像文件进行重命名。
