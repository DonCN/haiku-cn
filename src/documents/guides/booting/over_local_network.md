# 本地网络远程启动Haiku

对于最近更新的 Haiku 版本，Haiku 根磁盘镜像可以通过本地网络远程启动。当在某个平台系统引导时出现内核错误，需要进行错误跟踪时尤为有用。

在下述示例中，我们将介绍在不同平台上远程引导 Haiku。目前，这些操作主要面向开发人员。

## 前期准备

需求：Linux 构建工具
需求：Haiku 源码和构建环境
需求：测试系统需要足够的内存。（haiku.image 大小，以及另外 256MB 内存）
需求：由 `jam -q haiku-image` 生成的 haiku.image 镜像文件。
可选：TFTP 服务器（用于在某些系统上推送引导加载器，即 boot loader）。

## 远程磁盘服务器

Haiku 源码包含了一个通过 Haiku 引导管理器监听 UDP 请 求的远程磁盘服务器。当引导加载器接收到对磁盘镜像的 UDP 请求时，它会通过网络为测试系统提供远程镜像。

* 生成远程磁盘服务器

    使用 jam 工具可以非常直接的启动远程磁盘服务器。远程磁盘服务器必须在测试系统引导加载器启动之前进行启动。

    <pre>
    $ jam -q run ':remote_disk_server' ./generated/haiku.image
    ...found 109 target(s)...
    ...updating 1 target(s)...
    RunCommandLine1 run_0
    </pre>
	
    远程磁盘服务器可以使用 ·CTL+c· 予以关闭，并且每次 haiku 镜像修改之后，都需要重新生成。

* 远程磁盘服务器请求

    在远程磁盘服务器接收到请求之后，它会把可供使用的磁盘镜像推送到测试系统上的 Haiku 引导加载器...
    <pre>
    HELLO request
    READ request: offset: 0, 512 bytes    
    READ request: offset: 1024, 512 bytes    
    READ request: offset: 2048, 512 bytes    
    READ request: offset: 3072, 512 bytes    
    READ request: offset: 4096, 512 bytes    
    READ request: offset: 5120, 512 bytes    
    READ request: offset: 6144, 512 bytes    
    READ request: offset: 7168, 512 bytes    
    READ request: offset: 0, 512 bytes    
    READ request: offset: 67108864, 232 bytes    
    READ request: offset: 67110912, 1024 bytes    
    READ request: offset: 67111936, 1024 bytes    
    READ request: offset: 67110912, 1024 bytes    
    READ request: offset: 67111936, 1024 bytes
    </pre>	
## 启动系统

对于不同的平台，引导的方法各有不同。通常在 Haiku 菜单出现之前，引导加载器需要搜索远程磁盘镜像。

远程磁盘启动的复杂度也因平台而各异。下述平台在本文编写时都是可供使用的，但是你所面临的情况可能会有所不同。

* x86

    Haiku 引导加载器的 x86 版本可以通过 PXE 服务器进行引导。

    * PXE 引导

        生成x86 PXE 引导加载器...
		
        `TARGET_BOOT_PLATFORM=pxe_ia32 jam -q pxehaiku-loader haiku-netboot-archive`
        
        生成的镜像可以通过 DHCP 进行引导，将生成的两个文件放在 TFTP 服务器，并且通过 DHCP 的 “next-server/filename” 选项设置引导加载器。

* PowerPC

    Haiku 的 PowerPC 版本可以直接通过 TFTP 或者 CD 进行启动。

	* TFTP 引导

	    复制 boot_loader_openfirmware 引导文件到构建系统的 TFTP 共享。
	    
	    根据 OpenFirmware 的提示，执行下述命令，将 TFTP_SERVER_IP 替换为 tftp 服务器 IP 地址。同时，您也可以通过 MY_IP_ADDRESS 指定测试系统的客户端地址。

            `boot enet:TFTP_SERVER_IP,boot_loader_openfirmware<,MY_IP_ADDRESS>`
