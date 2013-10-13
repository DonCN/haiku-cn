# QEMU虚拟运行Haiku

[QEMU](http://www.qemu.org/) 是开源的系统模拟器。

启动虚拟机非常简单，如下所示：

`qemu ./generated/haiku.image`

从CD镜像开始引导：

`qemu -boot d -cdrom path/to/image.iso`