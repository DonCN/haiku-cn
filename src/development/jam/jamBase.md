# JamBase 参考

Jamebase 是 Jam 规则的一个基本子集，为 jam，也就是 Jam 可执行程序提供了一个类似于 make 的功能。本文可以作为 Jambase 的手册页，可作为 Jamfiles 中所使用使用的在 Jambase 中定义的规则，预制目标以及变量等的参考指南。

更多信息可以参考：
* Jamfiles 和 Jambase 的使用
* Jam 可执行程序

Jam 文档和源程序可以从 Perforce Public Depot 获取。更多有关下述规则的详细信息，可以参考 Jambase 文件。

## Jambase 规则

* As obj.o : source.s ;
    汇编source.s文件，根据Object规则调用。
* Bulk directory : sources ;
    将sources拷贝到directory目录。
* Cc object : source ;
    将 source 文件编译为 object ，所使用的编译器为 $(CC)，编译参数为 $(CCFLAGS) 和 $(OPTIM)，头文件目录为 $(HDRS)。根据Object规则调用。
* C++ obj.o : source.cc ;
    编译 C++ 文件source.cc 。根据 Object 规则调用。
* Chmod target ;
    （仅适用于 Unix 和 VMS。）修改 target 的文件权限为目标对应的值 $(MODE)，如链接，文件，安装文件，以及 Shell 脚本。
* Clean clean : targets ;
    Removes existing?targets?when?clean?is built. clean is not a dependency of all, and must be built explicitly for targets to be removed.(在构建clean时，删除存在的targets对象。Clean并不是所有)
* Fdefines defines ;
    Expands a list of definitions into a list of compiler (or preprocessor) switches (such as -Dsymbol=val?on Unix) to pass the definitions.
* File target : source ;
    将 source 拷贝至 target。
* Fincludes dirs ;
    Expands a list of directories into a list of compiler (or preprocessor) switches (such as -Idir?on Unix) to add the directories to the header inclusion search path.
* Fortran obj.o : source.f ;
    编译 Fortran 源文件 source.f。根据 Object 规则调用。
* FQuote files ;
    Returns each of?files?suitably quoted so as to hide shell metacharacters (such as whitespace and filename matching wildcards) from the shell.
* GenFile target : image sources ;
    运行命令 “image target sources” 从 sources 和 image 创建 target。（image 是根据 Main 规则构建的可执行文件）
* HardLink target : source ;
    创建到 source 的硬链接 target，如果该链接不存在的话。(仅适用于 Unix)
* HdrRule source : headers ;
    当 source 源文件通过 `#include` 预处理方式包含 headers 头文件时，排列适当的依赖关系。
    该规则并不显式的调用。当根据 Object 规则处理源文件，扫描头文件时自动的进行调用（例如，Main 或者 Library 规则中的源码）。
* InstallBin dir : sources ;
    使用 $(EXEMODE) 模式拷贝 sources 到 dir 目录。
* InstallLib dir : sources ;
    使用 $(FILEMODE) 模式拷贝 sources 到 dir 目录。
* InstallMan dir : sources ;
    使用 $(FILEMODE) 模式拷贝 sources 到 dir 相应的子目录。子目录为 mans，而 s 是每个源文件的后缀。
* InstallShell dir : sources ;
    使用 $(SHELLMODE) 模式拷贝 sources 到 dir 目录。
* Lex source.c : source.l
    处理 lex(1) 源文件 source.l，并且将 lex.yy.c 重命名为 source.c。根据 Object 规则调用。
* Library library : sources ;
    编译 sources 源文件，并将其打包为 library 库。生成的中间文件 objects 将被删除。调用 Objects 和 LibraryFromObjects。
    如果 Library 没有使用 library 作为后缀，那么将会使用 $(SUFLIB) 前缀。
* LibraryFromObjects library : objects ;
    打包 objects 为 library 库，之后删除 objects 文件。
    如果 library 没有前缀，则使用 $(SUFLIB) 后缀
* Link image : objects ;
    将目标文件 objects 链接为 image 文件，并修改 image 的权限为 $(EXEMODE)。Image 必须是实际的文件名；不需要提供后缀。由 Main 调用。
* LinkLibrary image : libraries ;
    根据 libraries 构建 image，在链接时将其包含进去。
    在本规则中，image 引用时不需要知名后缀；LinkLibraries 需要提供后缀。
* Main image : sources ;
    编译 sources 文件，并将它们链接为 image。调用 Objects 和 MainFromObjects。
    在本规则中，引用 image 时可以不提供后缀；Main 提供了这一后缀。
* MainFromObjects image : objects ;
    链接 objects 为 image。依赖于 exe。MainFromObjects 提供 image 文件名后缀。
* MakeLocate target : dir ;
    创建 dir 目录，并将 target 编译到 dir 目录。
* MkDir dir;
    创建 dir 目录以及其父目录。
* Object object : source ;
    编译 source 单个源文件为 object。Main 和 Library 规则使用该规则编译源文件
    由于源代码需要扫描 `#include` 所包含的目录，调用 HdrRule 创建 object 的所有包含文件依赖关系。
    根据源码后缀，调用下述规则进行编译：
        *.c: Cc
        *.cc: C++
        *.cpp: C++
        *.C: C++
        *.l: Lex
        *.y: Yacc
        *.*: UserObject

* ObjectC++Flags source : flags ;
* ObjectCcFlags source : flags ;
    在编译 source 时添加 flags 为 $(CCFLAGS) 或者 $(C++FLAGS) 的源码指定值。忽略所有 source 的文件后缀。
* ObjectDefines object : defines ;
    为 object 添加指定的 $(CCDEFS) 预处理符号定义。
* ObjectHdrs source : dirs ;
    在扫描和编译 source 时，根据 $(HDRS) 指定的值，添加 dirs 目录。
* Objects sources ;
    对于 sources 中的每个源文件，调用 Object 编译该源文件到相似命名的目标对象文件。
* RmTemps targets : sources ;
    使用 TEMPORARY 规则标记 sources 为临时对象，并在 targets 构建完成后删除 sources 文件。该规则必须最后作用于 targets。在 LibraryFromObjects 规则内部使用。
* Setuid images ;
    在链接完成后，设置 images 中每个文件的 setuid 标志位。(仅适用于 Unix)
* SoftLink target : source ;
    将 target 软链接到 source，如果它并不存在。（仅适用于 Unix）
* SubDir Top d1 … dn ;
    Sets up housekeeping for the source files located in?$(TOP)/d1/.../dn:
    Reads in rules file associated with?TOP, if it hasn't already been read.
    Initializes variables for search paths, output directories, compiler flags, and grist, using?d1 ... dn?tokens.
    TOP?is the name of a variable;?d1?thru?dn?are elements of a directory path.
* SubDirC++Flags flags ;
* SubDirCcFlags flags ;
    为 SubDir 目录中的源文件添加 flags 编译参数。
* SubDirHdrs d1 … dn ;
    为 SubDir 目录下的源文件添加路径 d1/…/dn/ 作为头文件扫描路径。 dl 至 dn 作为目录路径的元素。
* SubInclude VAR d1 … dn ;
    读取 $(VAR)/d1/…/dn 中的 Jamfile。
* Shell image : source ;
    拷贝 source 到可执行 shell 脚本 image。需要确保脚本的首行是 $(SHELLHEADER) (默认为 `#!/bin/sh`)。
* Undefines images : symbols ;
    在对 images 执行链接命令时，添加标志标记 symbols 为未定义的。Images 引用时可以不带后缀；Undefines 规则支持后缀描述。
* UserObject object : source ; 
    该规则由 Object 规则调用，用于未知后缀的源文件，需要在 Jamrules 中定义，并且对于未被 Object 规则处理的源文件类型，需要用户提供自定义的规则。在 Jambase 中的 UserObject 规则遇到未识别的原文件后缀时，它将会发出警告。
* Yacc source.c : source.y ;
    处理 yacc(1) 文件 source.y，并将产生的 y.tab.c 和 y.tab.h 重命名为 source.c 和 source.h。由 Object 规则调用。

## Jambase 伪对象(Pseudotargets)

Jam 目标对象有两种类型的：文件目标对象和伪目标对象。文件目标对象是可以在文件系统中找到。伪目标对象是象征性的（symbolic），并且通常表示其他目标。多数 Jambase 规则定了文件目标对象同时，也定义了依赖于目标文件类型的伪目标对象。Jambase 的伪目标对象如下：

* exe		由 Main 或 MainFromObjects 规则链接生成的可执行文件。
* lib		由 Library 或者 LibraryFromObjects 规则创建的库文件。
* obj		用于创建 Main 或者 Library 目标对象的编译中间对象。
* dirs		目标文件的写入目录。
* file		由 File 和 Bulk 规则拷贝的文件。
* shell		由 Shell 规则拷贝的文件
* clean 	删除所有的构建对象（出了被 Install* 规则所拷贝的文件）
* install	由 Install* 规则拷贝的文件。
* uninstall 删除 Install* 规则拷贝的所有目标文件。

另外，Jambase 依赖于 “exe”，“lib”，“obj”，“files”，和 “shell” 来创建 jam 的默认目标 “all”。

## Jambase 变量

下述变量在不同的平台上都有默认的值，参考 Jambase 文件来获取这些默认参数。

* ALL_LOCATE_TARGET
    构建目标对象的首选位置。默认情况下，Jambase 规则将构建对象放置在源码树。在 Jamrules 中设置 %(ALL_LOCATE_TARGET)，您可以将 jam 产生的所有目标对象写入源码树之外的位置。
* AR
    用于更新 Library 和 LibraryFromObjects 目标对象的打包命令。
* AS
    用于 As 规则目标对象的汇编工具。
* ASFLAGS
    提交到汇编工具 As 的汇编参数。
* AWK
    Awk 解释器的名称，在 Shell 规则拷贝 shell 脚本时使用。
* BCCROOT
    在 NT 平台上选择 Borland 编译器和链接工具。
* BINDIR
    目前已经不再使用。（例如，仅用于保持对 INSTALLBIN 规则的向后兼容。）
* CC
    用于 Cc 规则目标对象的 C 编译器。
* CCFLAGS
    用于 Cc 规则目标对象的编译参数。Cc 规则设置指定对象的 $(CCFLAGS) 值。
* C++
    用于 C++ 规则目标对象的 C++ 编译器。
* C++FLAGS
    用于 C++ 规则目标对象的编译参数。C++ 规则根据其对象设置指定的 $(C++FLAGS)。
* CHMOD
    Chmod 规则用于设置文件权限的程序 (通常为 chmod(1))。
* CP
    File 和Install* 规则中使用的文件拷贝程序。
* CRELIB
    如果设置该参数，Library 规则在尝试打包任何成员之前将会对目标库调用 CreLib 规则，这样就可以根据需要创建库。
* CW
    在 Macintosh 上，Code Warrior Pro 5 目录的根目录。
* DEFINES
    用于 Cc 和 C++ 规则对象的预处理符号定义。Cc 和 C++ 规则根据各自的目标对象在 $(DEFINES) 基础上，指定 $(CCDEFS)。（当然这里的先决条件是需要支持具有设置符号的命令行语法的编译器，例如 VMS）
* DOT
    当前目录在指定系统平台上的名称。
* DOTDOT
    父目录在指定平台上的名称。

## 备注

版权所有 1993-2002 Christopher Seiwald 和 Perforce Software, Inc. 

详情致信 info@perforce.com 

最近更新: Dec 31, 2000 
