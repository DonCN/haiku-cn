# 混合 GCC

## 什么是混合 GCC

在修改集 25536 中，Haiku 的构建可以实现 GCC 混合。在这种条件下，Haiku 能够同时支持使用 gcc2.95 和 gcc4.x 编译的运行库。在修改集 30875 中，它能够使用混合 gcc 编译任一类型的对象。

在混合 GCC 中，具有一个主 GCC 版本和一个可选 GCC 版本。主 GCC 版本用于编译 Haiku。而可选的 GCC 则为其他 GCC 对象的创建和使用提供了交叉编译器和运行时环境。编译器的切换可以使用一个简单的命令：setgcc -h。

由于 x86 是 BeOS R5 唯一可以实现二进制兼容的平台，因此其他的平台不需要使用 GCC2 进行构建。这样，x86 就是唯一需要使用混合 GCC 的平台。

## 使用哪个 GCC 版本

简而言至，就是混合 GCC2。对于 R1 和早期的版本，GCC2 混合版本是官方版本的要求。因此您所面对的问题数量会最少。

Haiku 处理库和相关文件的方式决定了，在混合 GCC2 版本下更容易的运行 BeOS R5 的程序。例如，在 GCC2 混合版本中，基于 GCC2 构建的库位于 */lib/，而基于 GCC4 构建的库则位于 */lib/gcc4/。由于 BeOS 软件不遵循该约定，它会毫无顾虑的将自己的库放在 */lib/。这样的结果就是，那些不遵循该约定的软件可能会出现错误。

## 为什么不使用 GCC4

虽然 GCC4 具有最强的优化算法，但是使用 GCC4 来进行 Haiku 的编译所带来的性能提升几乎没有。而且更重要的是，对于 R1 和早期版本，只有 GCC2 的 ABI 被认为是稳定的，并且需要进一步的验证。一旦 R1 发布，那些基于 GCC4 的库就需要重新编译（而不仅仅是更新它们的源码以适应新的 API）。

## 混合 GCC 如何构建

### 设置目录

    /path/haiku/buildtools/
    /path/haiku/haiku
    /path/haiku/generated.x86gcc2
    /path/haiku/generated.x86gcc4

### 配置目录

确保您已经非常了解各种配置参数，例如：·--use-gcc-pipe·，·--use-xattr·，和 ·-j·

### 基于 Haiku

如果使用 Haiku 来构建混合 GCC，则同时需要 GCC 2.95 和 GCC 4.x 编译器。如此，则最容易使用混合 GCC。否则您将需要手动的安装缺失的编译器。查阅可选包的代码块，那里会揭示编译的的位置。

    cd generated.x86gcc4
    ../configure --alternative-gcc-output-dir ../generated.x86gcc2 --cross-tools-prefix /boot/develop/abi/x86/gcc4/tools/current/bin/
    cd ..
    cd generated.x86gcc2
    ../configure --alternative-gcc-output-dir ../generated.x86gcc4 --cross-tools-prefix /boot/develop/abi/x86/gcc2/tools/current/bin/
    cd ..  
  
### 基于其他系统

    cd generated.x86gcc2
    ../configure --alternative-gcc-output-dir ../generated.x86gcc4 --build-cross-tools ../../buildtools/
    cd ..
    cd generated.x86gcc4
    ../configure --alternative-gcc-output-dir ../generated.x86gcc2 --build-cross-tools-gcc4 x86 ../../buildtools/
    cd ..

### 在生成的文件夹中使用 Jam

首先确保参考不同的 jam 选项。

### 以 GCC4.x 作为附加库的 GCC2.95 版本（即 x86gcc2hybird）

    cd generated.x86gcc2
    jam -a @nightly-raw

### 以 GCC2.95 作为附加库的 GCC4.x 版本（即 x86gcc4hybird）

    cd generated.x86gcc4
    jam -q @nightly-raw

### 仅 GCC2.95 版本

    cd generated.x86gcc2
    jam -q -sHAIKU_ADD_ALTERNATIVE_GCC_LIBS=0 @nightly-raw

### 仅 GCC4 的版本

    cd generated.x86gcc4
    jam -1 -sHAIKU_ADD_ALTERNATIVE_GCC_LIBS=0 @nightly-raw

如果没有预先配置，在生成的相应目录中将无法构建混合版本。这可以通过设置变量 HAIKU_ADD_ALTERNATIVE_GCC_LIBS=0 来完成
