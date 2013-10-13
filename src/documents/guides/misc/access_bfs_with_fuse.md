# 在Haiku以外访问BFS

何谓 FUSE？FUSE 是“Filesystem In USErspace” 的简称，其本质就是允许操作系统通过用户级程序访问文件系统。通过在内核空间以外提供此项功能，添加对新文件系统的支持就会非常简单，只需要安装对应的 FUSE 模块即可。相比而言，典型的范例就是让操作系统内核支持文件系统。更多信息，请访问 FUSE 项目主页。

BFS 的 FUSE 模块的最初实现在 r31409 版本。

## 准备

### 基于APT的GNU/Linux版本（Debian，Ubuntu等）

`sudo apt-get install libfuse-dev`

### 基于BSD的发行版

`sudo portinstall sysutils/fusefs-kmod sysutils/fusefs-libs`

## 从源码代码构建BFS的FUSE模块

<pre>
	cd /path/haiku/haiku/
	jam \<build\>bfs_fuse
</pre>	

## 挂载BFS分区

在本示例中，/dev/sdaX 是希望挂载的 BFS 分区。

<pre>
mkdir /path/to/mountPoint
/path/to/bfs_fuse /dev/sdaX /path/to/mountPoint
</pre>

如上操作，您的 BFS 分区将会挂载在 /path/to/mountPoint。