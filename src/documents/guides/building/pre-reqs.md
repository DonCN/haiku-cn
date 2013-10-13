# 预备软件

根据您所使用平台的不同，在为 Haiku 编译软件或者编译 Haiku 自身时，您可能需要额外的工具。

目前，可以认为 Haiku 是最方便的开发环境。一个 Haiku 的预编译测试版本将会包含编译 Haiku 镜像的所有需要的工具。尽管 Haiku 不是最快速的编译环境，但是它能够即时的测试新编译的代码，而且还具有完成的预装工具链，可以减少让您犯愁的机会。

目前，Haiku 能够支持自身构建，以及其他平台的交叉编译。

## 构建平台支持情况

下述是常见的构建平台以及它们的支持状态。该列表并不是很完整，并且构建情况可能会随着它们新版本的发布而发生变化。通过下述列表，您能够找到更多构建设置的详细信息。

<table>
<tr>
<td bgcolor="#DDD"><b>Platform</b></td>
<td bgcolor="#DDD"><b>Package Manager</b></td>
<td bgcolor="#DDD"><b>Supported</b></td>
<td bgcolor="#DDD"><b>Skill Level</b></td>
<td bgcolor="#DDD"><b>Notes</b></td>
</tr>
<tr>
<td>Haiku</td>
<td>OptionalPackages</td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Beginner</td>
<td>Easiest build platform</td>
</tr>
<tr>
<td><a href="http://www.archlinux.org" target="_blank" rel="nofollow">Arch Linux</a></td>
<td><a href="#pacman" rel="nofollow">pacman</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Advanced</td>
<td>-</td>
</tr>
<tr>
<td>BeOS</td>
<td><a href="#beos_zeta" rel="nofollow">pkg</a></td>
<td bgcolor="#FFFBC6">No</td>
<td>-</td>
<td>Once upon a time..</td>
</tr>
<tr>
<td><a href="http://www.centos.org" target="_blank" rel="nofollow">CentOS</a></td>
<td><a href="#yum" rel="nofollow">rpm/yum</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Intermediate</td>
<td>-</td>
</tr>
<tr>
<td><a href="http://www.debian.org" target="_blank" rel="nofollow">Debian Linux</a></td>
<td><a href="#apt" rel="nofollow">apt</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Intermediate</td>
<td>Missing mkisofs</td>
</tr>
<tr>
<td><a href="http://www.fedoraproject.org" target="_blank" rel="nofollow">Fedora</a></td>
<td><a href="#yum" rel="nofollow">rpm/yum</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Beginner</td>
<td>-</td>
</tr>
<tr>
<td><a href="http://www.freebsd.org" target="_blank" rel="nofollow">FreeBSD</a></td>
<td><a href="#bsd" rel="nofollow">packages</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Advanced</td>
<td>Works very well</td>
</tr>
<tr>
<td><a href="http://linuxmint.com/" target="_blank" rel="nofollow">Linux Mint</a></td>
<td><a href="#apt" rel="nofollow">apt</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Beginner</td>
<td>Works very well</td>
</tr>
<tr>
<td>Mac OS X</td>
<td><a href="#osx" rel="nofollow">MacPorts</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Intermediate</td>
<td>Works</td>
</tr>
<tr>
<td><a href="http://www.netbsd.org" target="_blank" rel="nofollow">NetBSD</a></td>
<td><a href="#bsd" rel="nofollow">packages</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Advanced</td>
<td>Untested</td>
</tr>
<tr>
<td><a href="http://www.opensuse.org" target="_blank" rel="nofollow">OpenSUSE</a></td>
<td><a href="#zypper" rel="nofollow">rpm/zypper</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Intermediate</td>
<td>-</td>
</tr>
<tr>
<td><a href="http://www.redhat.com" target="_blank" rel="nofollow">RedHat Linux</a></td>
<td><a href="#yum" rel="nofollow">rpm/yum</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Intermediate</td>
<td>-</td>
</tr>
<tr>
<td>Solaris</td>
<td><a href="#solaris" rel="nofollow">solaris pkg</a></td>
<td bgcolor="#FFFBC6">No</td>
<td>-</td>
<td>-</td>
</tr>
<tr>
<td><a href="http://www.ubuntu.com" target="_blank" rel="nofollow">Ubuntu</a></td>
<td><a href="#apt" rel="nofollow">apt</a></td>
<td bgcolor="#E4FFDE">Yes</td>
<td>Beginner</td>
<td>Works very well</td>
</tr>
<tr>
<td>Windows</td>
<td><a href="#cygwin" rel="nofollow">Cygwin</a></td>
<td bgcolor="#FFFBC6">No</td>
<td>-</td>
<td>-</td>
</tr>
<tr>
<td>Zeta</td>
<td><a href="#beos_zeta" rel="nofollow">pkg</a></td>
<td bgcolor="#FFFBC6">No</td>
<td>-</td>
<td>Once upon a time..</td>
</tr>
</table>

下面是一些必要的工具，在下述不同的平台中都有特别的说明。

## 常用工具列表

* Git 客户端，用以拷贝项目源代码
* SSH 客户端，仅用于项目提交权限成员

## 编译构建工具要求

* autoconf
* automake
* makeinfo
* flex
* bison
* gawk
* zlib 开发包，用于编译 GCC4 构建工具

## 编译 Haiku 要求

* Haiku 交叉编译工具，仅用于非 Haiku 构建平台，如 Linux 等。
* Jam，在 Haiku 中使用了定制的 Jam 版本。(包含在构建工具，用于交叉编译)
* mkisofs，注意 genisoimage 不等同于 mkisofs。
* wget，用于下载附加软件包。
* Yasm (0.7.0 及更高版本)

## ARM 平台编译要求
* mkimage
* Mtools
* sfdisk
* dtc (设备树编译器)

## Linux 平台备注

Haiku 的成功构建可能还要求您所进行编译的文件系统对 xattr 的支持。不幸的是，目前您所能做的选择非常有限，例如 ext4 并不能有效的支持这一特性。因此在 Linux 下建议使用 XFS 和 ReiserFS 文件系统。

另外，这只是使用时的一种反馈机制，并且它在某些时候仍存在一些问题。

通过下面的介绍，您可以对几个发行版有更深入的了解。

## 基于 APT 的 GNU/Linux 发行版(Debian,Ubuntu...)

下面是一些需要安装到 Debian/Ubuntu 的软件包，它们需要使用 APT 和 sudo。

    sudo apt-get install subversion yasm autoconf automake texinfo flex bison gawk build-essential 
	
对于 ARM 平台移植，您可能还需要下述软件包：

    sudo apt-get install uboot-mkimage util-linux mtools

备注：64位版本可能还需要安装 “gcc-multilib” 和 “g++-multilib” 。通常 “gcc-multilib” 已经安装，因为它被 “libc6-dev-i386”。更多相关信息，查看配置参数：--use-32bit。

    sudo apt-get install gcc-multilib g++-multilib libc6-dev-i386
	
为了使用 xsttr 支持，一些版本可能还需要安装 “attr” 和 “attr-dev”。详情查阅配置参数：--use-xattr。


## pacman(Arch Linux)

下述软件包需要安装到 Arch Linux，其需要用到 pacman 。

    pacman -S base-devel bison git texinfo yasm openssh unzip
	
## Puppy Linux

Puppy Linux 是一个非常特别的 linux 发行版，它关注于减少硬件占用，同时创造一个易于使用的用户环境。

对于 Puppy4.2.1，可能需要安装或者从源码编译下述的软件。

* Devx421.sfs
* yasm，(预编译包)
* Less，(预编译包)
* Cdrtools，(预编译包)

## 基于 RPM 使用 YUM 的 GNU/Linux 发行版（Fedora，CentOS...）

下述软件包需要安装到 Fedora/CentOS，其需要使用 Yum (这部分仍存疑议，请反馈问题)。

    sudo yum install git yasm autoconf automake texinfo flex bison gcc gcc-c++ make glibc-devel zlib-devel

对于 Fedora，如果因为缺少 libsupc++ 而导致编译失败，您可能还需要安装 libstc++-static 包。

    sudo yum install libstdc++-static
	
备注：64 位版本需要一些 32 位版本的库

    sudo yum install glibc.i686 glibc-devel.i686

为了使用 xattr 支持，一些发行版可能还需要安装 “attr” 和 “attr-dev” 。详情参见配置选项：--use--xattr

## 基于 RPM 使用 zypper 的 GNU/Linux 发行版(OpenSUSE, SLES)

下述软件包需要安装到 OpenSUSE/SUSE 商业版，其使用了 zypper(本部分存在疑议，请反馈问题)。

    sudo zypper install git yasm autoconf automake texinfo flex bison gcc-c++ make glibc-devel zlib-devel

备注：64 位版本可能需要某些 32 位版本的库。

    sudo zypper install linux32 glibc.i686 glibc-devel.i686

为了使用 xattr 支持，某些发行版可能需要安装 “attr” 和 “attr-dev”。详情参见配置选项：--use-xattr



## 基于 BSD 的发行版

基于 Package 的安装：

    sudo pkg_add -r bison subversion yasm gawk texinfo cdrtools-devel wget u-boot mtools linuxfdisk

基于 Ports 的安装

sudo portinstall devel/bison devel/subversion devel/yasm lang/gawk print/texinfo sysutils/cdrtools-devel ftp/wget devel/u-boot emulators/mtools sysutils/linuxfdisk

## Mac OS X

Haiku 的构建需要大小写敏感文件系统。您可以使用磁盘管理工具(Disk Utility)创建大小写敏感磁盘镜像，然后用以保存 Haiku 源码树。

首先，安装 MacPorts(提供了标准的安装包)
关闭当前终端，然后打开新终端窗口，输入：

    sudo port install cdrtools gawk gnuregex wget yasm

（可能会有提示，需要您输入当前账户的管理员密码）

如果出现错误 “port: command not found”，可能是因为保存在 ~/.profile 的 MacPorts shell 配置无法被当前用于使用。
如果您使用的是 Bash，您可能会拥有一个 ~/.bash_profile 或 ~./bash_login 文件，而它阻止了 bash 读取~./profile。
检查 Bash 使用的文件（按照下面的顺序），然后将下述文本添加到指定文件。

    export PATH=/opt/local/bin:$PATH
    export MANPATH=$MANPATH:/opt/local/share/man
    export INFOPATH=$INFOPATH:/opt/local/share/info

如果您使用的是其他的 shell，请查阅相关的手册，找出登录时解析的文件，然后添加所需的命令。
之后，您可以重新尝试在新终端中运行 port install... 命令。

备注：ARM 分支还未被 OSX 所支持，MacPort 具有 mtools，但是仍缺少了 sfdisk。

## BeOS 和 Zeta

BeOS 和 Zeta 现在已经不是活跃的开发环境。在未来的某天，这些工具非常有可能需要由个人来提供更新。

* Git:当前不可用
* SSH:[net_server,BONE]
* 开发工具(包含过时的 GCC 版本，请使用下述的版本)
* GCC compiler 2.95.3
* GCC Haiku cross compiler 2.95.3(解压安装到:/boot)
* Jam，2008-03-37
* yasm 0.8.0 (用于BONE)
* wget

## Cygwin

Cygwin是缺乏维护的开发环境。以下的说明由相应的社区提供。

* /community/forum/cygwin_nt_build_support
* /community/forum/how_to_get_haiku_running_in_virtualbox
* MauriceK's instructions

## Solaris

Solaris也是缺乏维护的开发环境。下面的说明也由相应的社区来提供。

* [openbeos] Building Haiku on Solaris... 摘抄自邮件列表