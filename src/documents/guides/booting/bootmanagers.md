# GRUB 配置

实现 Haiku 与其他系统共同引导非常容易。

## GRUB

GRUB (GRand Unified Bootloader) 是一个开源，常用于 Linux 系统引导，并且非常容易扩展的引导管理器。

从 os-prober v1.44 版本(如 Ubuntu 11.04 及后续版本)开始, Haiku 将能被自动识别。只需要运行 `sudo update-grub` 将 Haiku 添加到 GRUB 菜单即可。

## GRUB 2 (1.96 及更高版本)

在下述示例中，我们将进行如下设置：

* hd0 -- 首个硬盘
* hd0,1 -- 第一个硬盘首个分区 (sda1) Ubuntu Linux 根目录(/) 
* hd0,2 -- 第一个硬盘第二个分区(sda2) Ubuntu Linux 交换分区(swap)
* hd0,3 -- 第一个硬盘第三个分区(sda3) Haiku 分区

添加 Haiku 到您的 GRUB2 引导管理器，以及添加用于自动生成 GRUB2 菜单配置文件到配置块都非常简单。

如果之前在您的电脑上仅安装单个操作系统，在进行引导时，GRUB2 配置需要按下 Shift 键才可被激活，否则，由于 Haiku 无法自动识别为可引导系统，启动时将不会显示引导菜单。为了强制 GRUB2 每次都显示启动菜单，需要以下述方式添加 Haiku 选项，在 GRUB2 配置文件重新生成后，该选项将不被删除，执行步骤如下：

* 编辑 /etc/default/grub ，并确保注释掉 "GRUB_HIDDEN_TIMEOUT=0" 语句。
* 编辑 /etc/grub.d/40_custom 并添加如下内容：

    <pre>
    menuentry "Haiku R1A2" {
    set root=(hd0,3)
    chainloader +1
    }
    </pre>
	
    当然，选项 (h0,3) 需要指向安装了 Haiku 系统的分区。现在您可以通过如下命令重新生成引导菜单配置文件：

    <pre>
    sudo update-grub
    </pre>

## GRUB 早期版本 (0.97 及更早版本)

GRUB 早期版本与 GRUB2 的主要不同在于分区编号，其起始分区为 0 而不是 1。下述示例显示了 GRUB 早期版本的命名机制：

* hd0 -- 首个磁盘
* hd0,0 -- 第一个硬盘首个分区 (sda1) Ubuntu Linux 根目录(/) 
* hd0,1 -- 第一个硬盘第二个分区(sda2) Ubuntu Linux 交换分区(swap)
* hd0,2 -- 第一个硬盘第三个分区(sda3) Haiku 分区

与 GRUB2 相同，GRUB 早期版本的配置也很容易。在 Haiku 安装完成后，您需要启动 Linux 系统，在 /boot/grub/menu.lst 中添加下述代码：(这只是默认位置，您自己的配置可能会有所不同)

<pre>
# for Haiku
title Haiku R1A2
root (hd0,2)
chainloader +1
</pre>
