# Jamfiles 和 Jambase 的使用

本文描述了如何使用 Jam 的 Jambase 规则编写 Jamfiles 以便构建软件产品。相关的文档如下：

* Jam 可执行程序，描述了 jam 命令和 Jambase 中语言的使用。
* Jambase 参考，总结了 Jambase 规则和变量。

Jam 文档和源码可以从 [Perforce Public Depot](http://public.perforce.com/public/index.html) 获取。

## 概览

Jam，Jam 的可执行程序，使用依赖关系和 Jam 规则文件中定义的构建指令，从源代码递归的构建目标文件。Jam 解析规则文件，区别目标和源码，检查文件系统以确定哪些目标需要更新，然后执行系统命令更新目标。

基本的规则文件为 “Jambase”，由 Jam 发行程序提供。Jambase 文件定义了支持标准程序构建操作的规则和变量，如编译，链接，等等。

在使用 Jambase 规则时，jam 读取 Jambase，然后从当前目录中读取 “Jamfile” 文件。Jamfile 描述了其所在目录中源文件所需执行的操作。它可能也会导致读取其他目录中的 Jamfile。

在特定的环境中，首个 Jamfile 的读取会导致特定位置 “Jamrules” 文件的读取。Jamrules 文件是一个附加的规则和变量定义的集合，用于定义特定场合的处理。

### 基本的 Jamfile

Jamfile 包含规则调用，通常如下所示：

	RuleName targets : targets ;

冒号左侧的 target(s) 用于指明构建的结果，而左侧的 target(s) 通常指明构建的源文件。

Jamfile 也可以如下编写：

	Main myprog : main.c util.c ;
	
This specifies that there is a main.c and util.c file in the same directory as the Jamfile, and that those source files should be compiled and linked into an executable called myprog. If you cd to the directory where this Jamfile lives, you can see the exactly how jam would build myprog with:
	jam -n
Or, you can actually build myprog with the command:
	jam
	
### Whitespace

Jamfile elements are delimited by whitespace (blanks, tabs, or newlines). Elements to be delimited include rule names, targets, colons, and semicolons. A common mistake users make is to forget the whitespace, e.g.,
	Main myprog: main.c util.c ; #WRONG!
Jam doesn't distinguish between a typo and a target called "myprog:", so if you get strange results, the first thing you should check for in your Jamfile is missing whitespace.
Filenames, Target Identifiers, and Buildable Targets

Consider this Jamfile:

	Main myprog : main.c util.c ;                   
	LinkLibraries myprog : libtree ;     
	Library libtree : treemake.c treetrav.c ;   
	
The Main rule specifies that an executable called myprog will be built. The compiled main.c and util.c objects will be linked to produce myprog. The LinkLibraries rule specifies that libtree will be linked into myprog as well. The Library rule specifies which source files will be compiled and archived into the libtree library.

The Jamfile above refers to targets like "myprog" and "libtree". However, depending on the platform you're building on, the actual filenames of those targets could be "myprog.exe" and "libtree.lib". Most Jambase rules supply the actual filenames of targets, so that Jamfiles themselves need not make any platform-specific filename references.

The jam program builds up a list of unique target identifiers. Unless you are using the SubDir rules (described later), the default identifier for a file target is its filename. In the above example, the target identifiers are the filenames: myprog.exe, libtree.lib, main.obj, etc.

While all Jambase rules refer to "targets", not all targets are buildable. There are two kinds of buildable targets: file targets and pseudotargets. File targets are objects that can be found in the filesystem. Pseudotargets are symbolic, and represent other targets.

You can use any buildable target on the jam command line to build a subset of defined targets. For example:

    jam libtree.a 
on Unix builds the libtree library and all the compiled objects that go in it.

### Pseudotargets

Most Jambase rules that define file targets also define pseudotargets which are dependent on types of file targets. For example, Jambase defines a pseudotarget called "lib", which is dependent on file targets created by the Library rule. So the command:

    jam lib
	
used with the above example would cause the libtree library to be built. Also, there is one pseudotarget built into jam itself, called "all". Jambase sets "all" dependent on (almost) all other targets.
In the unfortunate case where you have a buildable target whose name is the same as one of the Jambase pseudotargets, you'll have problems with the conflicting target name. Your workaround choices are:

1. Change the name of your buildable file or directory that conflicts.
2. Modify your Jambase and change the name of the conflicting pseudotarget. (Pseudotargets are defined in Jambase using the NOTFILE rule.)
3. Use grist on the conflicting target name in your Jamfile. E.g., instead of

        File lib : libfoo.a ;
    
    try
        File <dir>lib : libfoo.a ;
    
### Dependencies

Jambase rules set dependencies on targets, so that if you update a source file, all the file targets that depend on that source file, and only the ones that depend on that source file, will be updated (rebuilt) the next time you run jam.

Here are some of the dependencies that get set when jam runs on NT using the example Jamfile above:

Target	   	Depends on
myprog.exe		main.obj, util.obj, libtree.lib
libtree.lib		treemake.obj, treetrav.obj
treetrav.obj		treetrav.c

Furthermore, the Main and Library rules set up recursive header scanning on their source targets. So after jam has finished parsing the Jamfile and setting the rule-driven dependencies, it scans the source files for "`#include`" lines. All `#include` files found during this scan become dependencies of the compiled object. E.g., all header files used to compile treetrav.c would be made dependencies of treetrav.obj.

As a result, when you run jam, it will rebuild targets if either the source files change or the header files change. You can't tell by looking at a Jamfile which header files are dependencies, but you can easily display those dependencies with:

	jam -nd+3
	
### Rule Ordering

Rules which specify dependencies, like the Main, Library, and LinkLibrary rules, can be invoked in any order. jam figures out the order in which targets are built from their dependencies.

Some rules, however, set variables which are used by subsequent rule invocations, and their ordering is important. For example, the SubDir* rules (discussed later) must be invoked in a particular order.

### Detailed Jambase Specifications

This document describes how to use various Jambase rules from a functional point of view. You can see the summary of available Jambase rules in the Jambase Reference. The detailed specifications for any Jambase rule can be found by reading the rule definition itself in the Jambase file.

## Handling Directory Trees

The SubDir* rules are used to define source code directory hierarchies. With SubDir and SubInclude, you can use jam to build software from source files and Jamfiles spread across many directories, as is typical for large projects. The SubDir* rules unify an entire source code tree so that jam can read in all the Jamfiles in one pass and compute dependencies across the entire project.
To use the SubDir* rules, you must:

1. Preface the Jamfile in each directory with an invocation of the SubDir rule.
2. Place at the root of the tree a file named Jamrules. This file could be empty, but in practice it contains user-provided rules and variable definitions that are shared throughout the tree. Examples of such definitions are library names, header directories, install directories, compiler flags, etc. This file is good candidate for automatic customizing with autoconf(GNU).
3. Optionally, set an environment variable pointing to the root directory of the srouce tree. The variable's name is left up to you, but in these examples, we use TOP.

### SubDir Rule

The SubDir rule must be invoked before any rules that refer to the contents of the directory - it is best to put it at the top of each Jamfile. For example:

	# Jamfile in $(TOP)/src/util directory.

	SubDir TOP src util ;

	Main myprog : main.c util.c ;                   
	LinkLibraries myprog : libtree ;     
	Library libtree : treemake.c treetrav.c ; 
	
This compiles four files in $(TOP)/src/util, archives two of the objects into libtree, and links the whole thing into myprog. Outputs are placed in the $(TOP)/src/util directory.
This doesn't appear to be any different from the previous example that didn't have a SubDir rule, but two things are happening behind the scenes:

1. The SubDir rule causes jam to read in the $(TOP)/Jamrules file. (The Jamrules file can alternately be named by the variable $(xxxRULES), where xxx is the name of the root variable, e.g., $(TOPRULES)).
The Jamrules file can contain variable definitions and rule definitions specific to your codeline. It allows you to completely customize your build environment without having to rewrite Jambase. Jamrules is only read in once, at the first SubDir invocation.
2. The SubDir rule initializes a set of variables that are used by Main and other rules to uniquely identify the source files in this directory and assign locations to the targets built from files in this directory.
When you have set a root variable, e.g., $(TOP), SubDir constructs path names rooted with $(TOP), e.g., $(TOP)/src/util. Otherwise,    SubDir constructs relative pathnames to the root directory, computed from the number of arguments to the first SubDir rule, e.g., ../../src/util. In either case, the SubDir rule constructs the path names that locate source files. You'll see how this is useful later.

The SubDir rule takes as its first argument the root variable's name and takes as subsequent arguments the directory names leading from the root to the directory of the current Jamfile. Note that the name of the subdirectory is given as individual elements: the SubDir rule does not use system-specific directory name syntax.

### SubInclude Rule

The SubInclude rule is used in a Jamfile to cause another Jamfile to be read in. Its arguments are in the same format as SubDir's.
The recommended practice is only to include one level of subdirectories at a time, and let the Jamfile in each subdirectory include its own subdirectories. This allows a user to sit in any arbitrary directory of the source tree and build that subtree. For example:

       # This is $(TOP)/Jamfile, top level Jamfile for mondo project.

       SubInclude TOP src ;
       SubInclude TOP man ;
       SubInclude TOP misc ;
       SubInclude TOP util ;
	   
If a directory has both subdirectories of its own as well as files that need building, the SubIncludes should be either before the SubDir rule or be at the end of the Jamfile - not between the SubDir and other rule invocations. For example:

	# This is $(TOP)/src/Jamfile:

	SubDir TOP src ;

	Main mondo : mondo.c ;
	LinkLibraries mondo : libmisc libutil ;
	
	SubInclude TOP src misc ;
	SubInclude TOP src util ;
	
(jam processes all the Jamfiles it reads as if it were reading one single, large Jamfile. Build rules like Main and LinkLibraries rely on the preceding SubDir rule to set up source file and output file locations, and SubIncludes rules read in Jamfiles that contain SubDir rules. So if you put a SubIncludes rule between a SubDir and a Main rule, jam will try to find the source files for the Main rule in the wrong directory.)

### Variables Used to Handle Directory Trees

The following variables are set by the SubDir rule and used by the Jambase rules that define file targets:
SEARCH_SOURCE		The SubDir targets (e.g., "TOP src util") are used to construct a pathname (e.g., $(TOP)/src/util), and that pathname is assigned to $(SEARCH_SOURCE). Rules like Main and Library use $(SEARCH_SOURCE) to set search paths on source files.
LOCATE_SOURCE		Initialized by the SubDir rule to the same value as $(SEARCH_SOURCE), unless ALL_LOCATE_TARGET is set. $(LOCATE_SOURCE) is used by rules that build generated source files (e.g., Yacc and Lex) to set location of output files. Thus the default location of built source files is the directory of the Jamfile that defines them.
LOCATE_TARGET		Initalized by the SubDir rule to the same value as $(SEARCH_SOURCE), unless ALL_LOCATE_TARGET is set. $(LOCATE_TARGET) is used by rules that build binary objects (e.g., Main and Library) to set location of output files. Thus the default location of built binaray files is the directory of the Jamfile that defines them.
ALL_LOCATE_TARGET		 If $(ALL_LOCATE_TARGET) is set, LOCATE_SOURCE and and LOCATE_TARGET are set to $(ALL_LOCATE_TARGET) instead of to $(SEARCH_SOURCE). This can be used to direct built files to be written to a location outside of the source tree, and enables building from read-only source trees.
SOURCE_GRIST		The SubDir targets are formed into a string like "src!util" and that string is assigned to SOURCE_GRIST. Rules that define file targets use $(SOURCE_GRIST) to set the "grist" attribute on targets. This is used to assure uniqueness of target identifiers where filenames themselves are not unique. For example, the target identifiers of $(TOP)/src/client/main.c and $(TOP)/src/server/main.c would be <src!client>main.c and <src!server>main.c.
The $(LOCATE_TARGET) and $(SEARCH_SOURCE) variables are used extensively by rules in Jambase: most rules that generate targets (like Main, Object, etc.) set $(LOCATE) to $(LOCATE_TARGET) for the targets they generate, and rules that use sources (most all of them) set $(SEARCH) to be $(SEARCH_SOURCE) for the sources they use.

$(LOCATE) and $(SEARCH) are better explained in The Jam Executable Program but in brief they tell jam where to create new targets and where to find existing ones, respectively.

Note that you can reset these variables after SubDir sets them. For example, this Jamfile builds a program called gensrc, then runs it to create a source file called new.c:

       SubDir TOP src util ;
       Main gensrc : gensrc.c ;
       LOCATE_SOURCE = $(NEWSRC) ;
       GenFile new.c : gensrc ;
       
By default, new.c would be written into the $(TOP)/src/util directory, but resetting LOCATE_SOURCE causes it to be written to the $(NEWSRC) directory. ($(NEWSRC) is assumed to have been set elsewhere, e.g., in Jamrules.)

### VMS Notes

On VMS, the logical name table is not imported as is the environment on UNIX. To use the SubDir and related rules, you must set the value of the variable that names the root directory. For example:
              TOP = USR_DISK:[JONES.SRC] ;

              SubInclude TOP util ;
The variable must have a value that looks like a directory or device. If you choose, you can use a concealed logical. For example:
              TOP = TOP: ;

              SubInclude TOP util ;
The : at the end of TOP makes the value of $(TOP) look like a device name, which jam respects as a directory name and will use when trying to access files. TOP must then be defined from DCL:
              $ define/job/translation=concealed TOP DK100:`[USERS.JONES.SRC.]`
Note three things: the concealed translation allows the logical to be used as a device name; the device name in the logical (here DK100) cannot itself be concealed logical (VMS rules, man); and the directory component of the definition must end in a period (more VMS rules).

## Building Executables and Libraries

The rules that build executables and libraries are: Main, Library, and LinkLibraries.

### Main Rule

The Main rule compiles source files and links the resulting objects into an executable. For example:
              Main myprog : main.c util.c ;
This compiles main.c and util.c and links main.o and util.o into myprog. The object files and resulting executable are named appropriately for the platform.
Main can also be used to build shared libraries and/or dynamic link libraries, since those are also linked objects. E.g.:

		Main driver$(SUFSHR) : driver.c ;
	
Normally, Main uses $(SUFEXE) to determine the suffix on the filename of the built target. To override it, you can supply a suffix explicity. In this case, $(SUFSHR) is assumed to be the OS-specific shared library suffix, defined in Jamrules with something like:
		if $(UNIX)      { SUFSHR = .so ; }
		else if $(NT)   { SUFSHR = .dll ; }
	
Main uses the Objects rule to compile source targets.

### Library Rule

The Library rule compiles source files, archives the resulting object files into a library, and then deletes the object files. For example:
              Library libstring : strcmp.c strcpy.c strlen.c ;
              Library libtree : treemake.c treetrav.c ;
This compiles five source files, archives three of the object files into libstring and the other two into libtree. Actual library filenames are formed with the $(SUFLIB) suffix. Once the objects are safely in the libraries, the objects are deleted.
Library uses the Objects rule to compile source files.

### LinkLibraries Rule

To link executables with built libraries, use the LinkLibraries rule. For example:
              Main myprog : main.c util.c ;
              LinkLibraries myprog : libstring libtree ;
The LinkLibraries rule does two things: it makes the libraries dependencies of the executable, so that they get built first; and it makes the libraries show up on the command line that links the executable. The ordering of the lines above is not important, because jam builds targets in the order that they are needed.
You can put multiple libraries on a single invocation of the LinkLibraries rule, or you can provide them in multiple invocations. In both cases, the libraries appear on the link command line in the order in which they were encountered. You can also provide multiple executables to the LinkLibraries rule, if they need the same libraries, e.g.:

		LinkLibraries prog1 prog2 prog3 : libstring libtree ;
       
### Variables Used in Building Executables and Libraries

* AR		Archive command, used for Library targets.
* SUFEXE	*	Suffix on filenames of executables referenced by Main and LinkLibraries.
* LINK		Link command, used for Main targets.
* LINKFLAGS		Linker flags.
* LINKLIBS		Link libraries that aren't dependencies. (See note below.)
* EXEMODE	*	File permissions on Main targets.
* MODE		Target-specific file permissions on Main targets (set from $(EXEMODE))
* RANLIB		Name of ranlib program, if any.

Variables above marked with "*" are used by the Main, Library, and LinkLibraries rules. Their values at the time the rules are invoked are used to set target-specific variables.

All other variables listed above are globally defined, and are used in actions that update Main and Library targets. This means that the global values of those variables are used, uness target-specific values have been set. (For instance, a target-specific MODE value is set by the Main rule.) The target-specific values always override global values.

* Note that there are two ways to specify link libraries for executables:

* Use the LinkLibraries rule to specify built libraries; i.e., libraries that are built by Library rules. This assures that these libraries are built first, and that Main targets are rebuilt when the libraries are updated.
Use the LINKLIBS variable to specify external libraries; e.g., system libraries or third-party libraries. The LINKLIBS variable must be set to the the actual link command flag that specifies the libraries.
For example:

	#In Jamrules:
              if $(UNIX) { X11LINKLIBS = -lXext -lX11 ; }
              if $(NT)   { X11LINKLIBS = libext.lib libX11.lib ; }

	#In Jamfile:
              Main xprog : xprog.c ;
              LINKLIBS on xprog$(SUFEXE) = $(X11LINKLIBS) ;
              LinkLibraries xprog : libxutil ;
              Library libxutil : xtop.c xbottom.c xutil.c ;
			  
This example uses the Jam syntax "variable on target" to set a target-specific variable. In this way, only xprog will be linked with this special $(X11LINKLIBS), even if other executables were going to be built by the same Jamfile. Note that when you set a variable on a target, you have to specify the target identifer exactly, which in this case is the suffixed filename of the executable. The actual link command line on Unix, for example, would look something like this:
              cc -o xprog xprog.o libxutil.a -lXext -lX11

## Compiling

Compiling of source files occurs normally as a byproduct of the Main or Library rules, which call the rules described here. These rules may also be called explicitly if the Main and Library behavior doesn't satisfy your requirements.

### Objects Rule

The Main and Library rules call the Objects rule on source files. Compiled object files built by the Objects rule are a dependency of the obj pseudotarget, so "jam obj" will build object files used in Main and Library rules.
Target identifiers created by the Objects rule have grist set to $(SOURCE_GRIST). So given this Jamfile:

		SubDir TOP src lock ;
		Main locker : lock.c ;
       
the object file created is lock.o (or lock.obj) and its target identifier is <src!lock>lock.o (or <src!lock>lock.obj).
You can also call Objects directly. For example:

              Objects a.c b.c c.c ;
This compiles a.c into a.o, b.c into b.o, etc. The object file suffix is supplied by the Objects rule.

### Object Rule

Objects gets its work done by calling the Object rule on each of the source files. You could use the Object rule directly. For example, on Unix, you could use:
              Object foo.o : foo.c ;
However, the Object rule does not provide suffixes, and it does not provide the grist needed to construct target identifiers if you are using the SubDir* rules. A portable and robust Jamfile would need to invoke Object thus:
	      Object <src!util>foo$(SUFOBJ) : <src!util>foo.c ;
	
which is inelegant and clearly shows why using Objects is better than using Object.
If there's any advantage to the Object rule, it's that it doesn't require that the object name bear any relationship to the source. It is thus possible to compile the same file into different objects. For example:

              Object a.o : foo.c ;
              Object b.o : foo.c ;
              Object c.o : foo.c ;
This compiles foo.c (three times) into a.o, b.o, and c.o. Later examples show how this is useful.
The Object rule looks at the suffix of the source file and calls the appropriate rules to do the actual preprocessing (if any) and compiling needed to produce the output object file. The Object rule is capable of the generating of an object file from any type of source. For example:

              Object grammar$(SUFOBJ) : grammar.y ;
              Object scanner$(SUFOBJ) : scanner.l ;
              Object fastf$(SUFOBJ) : fastf.f ;
              Object util$(SUFOBJ) : util.c ;
An even more elegant way to get the same result is to let the Objects rule call Object:
              Objects grammar.y scanner.l fastf.f util.c ;
	
In addition to calling the compile rules, Object sets up a bunch of variables specific to the source and target files. (See Variables Used in Compiling, below.)

### Cc, C++, Yacc, Lex, Fortran, As, etc. Rules

The Object rule calls compile rules specific to the suffix of the source file. (You can see which suffixes are supported by looking at the Object rule definition in Jambase.) Because the extra work done by the Object rule, it is not always useful to call the compile rules directly. But the adventurous user might attempt it. For example:

              Yacc grammar.c : grammar.y ;
              Lex scan.c : scan.l ;
              Cc prog.o : prog.c ;
These examples individually run yacc(1), lex(1), and the C compiler on their sources.

### UserObject Rule

Any files with suffixes not understood by the Object rule are passed to the UserObject rule. The default definition of UserObject simply emits a warning that the suffix is not understood. This Jambase rule definition is intended to be overridden in Jamrules with one that recognizes the project-specific source file suffixes. For example:

	#In Jamrules:

              rule UserObject
              {
                  switch $(>)
                  {
                  case *.rc   : ResourceCompiler $(<) : $(>) ;
                  case *      : ECHO "unknown suffix on" $(>) ;
                  }
              }

              rule ResourceCompiler
              {
                  DEPENDS $(<) : $(>) ;
		  Clean clean : $(<) ;
              }

              actions ResourceCompiler
              {
                  rc /fo $(<) $(RCFLAGS) $(>)
              }


	#In Jamfile:

              Library liblock : lockmgr.c ;
	      if $(NT) { Library liblock : lock.rc ; }
		  
In this example, the UserObject definition in Jamrules allows *.rc files to be handle as regular Main and Library sources. The lock.rc file is compiled into lock.obj by the "rc" command, and lock.obj is archived into a library with other compiled objects.

### LibraryFromObjects Rule

Sometimes the Library rule's straightforward compiling of source into object modules to be archived isn't flexible enough. The LibraryFromObjects rule does the archiving (and deleting) job of the Library rule, but not the compiling. The user can make use of the Objects or Object rule for that. For example:
              LibraryFromObjects libfoo.a : max.o min.o ;
              Object max.o : maxmin.c ;
              Object min.o : maxmin.c ;
              ObjectCcFlags max.o : -DUSEMAX ;
              ObjectCcFlags min.o : -DUSEMIN ;
This Unix-specific example compiles the same source file into two different objects, with different compile flags, and archives them. (The ObjectCcFlags rule is described shortly.) Unfortunately, the portable and robust implementation of the above example is not as pleasant to read:
	      SubDir TOP foo bar ;
              LibraryFromObjects libfoo$(SUFLIB) : <foo!bar>max$(SUFOBJ) 
			                           <foo!bar>min$(SUFOBJ) ;
              Object <foo!bar>min$(SUFOBJ) : <foo!bar>maxmin.c ;
              Object <foo!bar>max$(SUFOBJ) : <foo!bar>maxmin.c ;
	      ObjectCcFlags <foo!bar>min$(SUFOBJ) : -DUSEMIN ;
	      ObjectCcFlags <foo!bar>max$(SUFOBJ) : -DUSEMAX ;
       
Note that, among other things, you must supply the library file suffix when using the LibraryFromObjects rule.

### MainFromObjects Rule

Similar to LibraryFromObjects, MainFromObjects does the linking part of the Main rule, but not the compiling. MainFromObjects can be used when there are no objects at all, and everything is to be loaded from libraries. For example:
              MainFromObjects testprog ;
              LinkLibraries testprog : libprog ;
              Library libprog : main.c util.c ;
On Unix, say, this generates a link command that looks like:
              cc -o testprog libprog.a
Linking purely from libraries is something that doesn't work everywhere: it depends on the symbol "main" being undefined when the linker encounters the library that contains the definition of "main".

### Variables Used in Compiling

The following variables control the compiling of source files:

* C++		The C++ compiler command
* CC		The C compiler command
* C++FLAGS 
* CCFLAGS		Compile flags, used to create or update compiled objects
* SUBDIRC++FLAGS
* SUBDIRCCFLAGS		Additonal compile flags for source files in this directory.
* OPTIM		Compiler optimization flag. The Cc and C++ actions use this as well as C++FLAGS or CCFLAGS.
* HDRS		Non-standard header directories; i.e., the directories the compiler will not look in by default and which therefore must be supplied to the compile command. These directories are also used by jam to scan for include files.
* STDHDRS		Standard header directories, i.e., the directories the compiler searches automatically. These are not passed to the compiler, but they are used by jam to scan for include files.
* SUBDIRHDRS		Additional paths to add to HDRS for source files in this directory.
* LEX		The lex(1) command
* YACC		The yacc(1) command

The Cc rule sets a target-specific $(CCFLAGS) to the current value of $(CCFLAGS) and $(SUBDIRCCFLAGS). Similarly for the C++ rule. The Object rule sets a target-specific $(HDRS) to the current value of $(HDRS) and $(SUBDDIRHDRS).

$(CC), $(C++), $(CCFLAGS), $(C++FLAGS), $(OPTIM), and $(HDRS) all affect the compiling of C and C++ files. $(OPTIM) is separate from $(CCFLAGS) and $(C++FLAGS) so they can be set independently.

$(HDRS) lists the directories to search for header files, and it is used in two ways: first, it is passed to the C compiler (with the flag -I prepended); second, it is used by HdrRule to locate the header files whose names were found when scanning source files. $(STDHDRS) lists the header directories that the C compiler already knows about. It does not need passing to the C compiler, but is used by HdrRule.

Note that these variables, if set as target-specific variables, must be set on the target, not the source file. The target file in this case is the object file to be generated. For example:

              Library libximage : xtiff.c xjpeg.c xgif.c ;

              HDRS on xjpeg$(SUFOBJ) = /usr/local/src/jpeg ;
              CCFLAGS on xtiff$(SUFOBJ) = -DHAVE_TIFF ;
This can be done more easily with the rules that follow.

### ObjectCcFlags, ObjectC++Flags, ObjectHdrs Rules

$(CCFLAGS), $(C++FLAGS) and $(HDRS) can be set on object file targets directly, but there are rules that allow these variables to be set by referring to the original source file name, rather than to the derived object file name. ObjectCcFlags adds object-specific flags to the $(CCFLAGS) variable, ObjectC++Flags adds object-specific flags to the $(C++FLAGS) variable, and ObjectHdrs add object-specific directories to the $(HDRS) variable. For example:
	#In Jamrules:
		if $(NT) { CCFLAGS_X = /DXVERSION ;	
			   HDRS_X = \\\\SPARKY\\X11\\INCLUDE\\X11 ;
		         }

	#In Jamfile:
              Main xviewer : viewer.c ;
              ObjectCcFlags viewer.c : $(CCFLAGS_X) ;
              ObjectHdrs viewer.c : $(HDRS_X) ;
The ObjectCcFlags and ObjectHdrs rules take .c files as targets, but actually set $(CCFLAGS) and $(HDRS) values on the .obj (or .o) files. As a result, the action that updates the target .obj file uses the target-specific values of $(CCFLAGS) and $(HDRS).

### SubDirCcFlags, SubDirC++Flags, SubDirHdrs Rules

These rules set the values of $(SUBDIRCCFLAGS), $(SUBDIRC++FLAGS) and $(SUBDIRHDRS), which are used by the Cc, C++, and Object rules when setting the target-specific values for $(CCFLAGS), $(C++FLAGS) and $(HDRS). The SubDir rule clears these variables out, and thus they provide directory-specific values of $(CCFLAGS), $(C++FLAGS) and $(HDRS). For example:
	#In Jamrules:
              GZHDRS = $(TOP)/src/gz/include ;
	      GZFLAG = -DGZ ;
		
	#In Jamfile:
              SubDir TOP src gz utils ;

              SubDirHdrs $(GZHDRS) ;
              SubDirCcFlags $(GZFLAG) ;

	      Library libgz : gizmo.c ;
	      Main gizmo : main.c ;
	      LinkLibraries gizmo : libgz ;
All .c files in this directory files will be compiled with $(GZFLAG) as well as the default $(CCFLAG), and the include paths used on the compile command will be $(GZHDRS) as well as the default $(HDRS).

## Header File Processing

One of the functions of the Object rule is set up scanning of source files for (C style) header file inclusions. To do so, it sets the special variables $(HDRSCAN) and $(HDRRULE) as target-specific variables on the source file. The presence of these variables triggers a special mechanism in jam for scanning a file for header file inclusions and invoking a rule with the results of the scan. The $(HDRSCAN) variable is set to an egrep(1) pattern that matches "#include" statements in C source files, and the $(HDRRULE) variable is set to the name of the rule that gets invoked as such:
              $(HDRRULE) source-file : included-files ;
This rule is supposed to set up the dependencies between the source file and the included files. The Object rule uses HdrRule to do the job. HdrRule itself expects another variable, $(HDRSEARCH), to be set to the list of directories where the included files can be found. Object does this as well, setting $(HDRSEARCH) to $(HDRS) and $(STDHDRS).
The header file scanning occurs during the "file binding" phase of jam, which means that the target-specific variables (for the source file) are in effect. To accomodate nested includes, one of the HdrRule's jobs is to pass the target-specific values of $(HDRRULE), $(HDRSCAN), and $(HDRSEARCH) onto the included files, so that they will be scanned as well.

### HdrRule Rule

Normally, HdrRule is not invoked directly; the Object rule (called by Main and Library) invokes it.
If there are special dependencies that need to be set, and which are not set by HdrRule itself, you can define another rule and let it invoke HdrRule. For example:

	#In Jamrules:
              rule BuiltHeaders
              {
                      DEPENDS $(>) : mkhdr$(SUFEXE) ;
                      HdrRule $(<) : $(>) ;
              }

	#In Jamfile:
              Main mkhdr : mkhdr.c ;
              Main ugly : ugly.c ;

              HDRRULE on ugly.c = BuiltHeaders ;

This example just says that the files included by "ugly.c" are generated by the program "mkhdr", which can be built from "mkhdr.c". During the binding phase, jam will scan ugly.c, and if it finds an include file, ughdr.h, for example, it will automatically invoke the rule:
              BuiltHeaders ugly.c : ughdr.h ;
       
By calling HdrRule at the end of BuiltHeaders, all the gadgetry of HdrRule takes effect and it doesn't need to be duplicated.

### Variables Used for Header Scanning

* HDRPATTERN		Default scan pattern for "include" lines.
* HDRSCAN		Scan pattern to use. This is a special variable: during binding, if both HDRSCAN and HDRRULE are set, scanning is activated on the target being bound. The HdrRule and Object rules sets this to $(HDRPATTERN) on their source targets.
* HDRRULE		Name of rule to invoked on files found in header scan. The HdrRule and Object rules set this to "HdrRule" on their source targets. This is also a special variable; it's the only jam variable that can hold the name of a rule to be invoked.
* HDRSEARCH		Search paths for files found during header scanning. This is set from $(HDRS) and $(STDHDRS), which are described in the Compiling section. jam will search $(HDRSEARCH) directories for the files found by header scans.

The Object rule sets HDRRULE and HDRSCAN specifically for the source files to be scanned, rather than globally. If they were set globally, jam would attempt to scan all files, even library archives and executables, for header file inclusions. That would be slow and probably not yield desirable results.

## Copying Files

### File Rule

The File rule copies one file to another. The target name needn't be the same as the source name. For example:
	switch $(OS)
	{
           case NT*  : File config.h : confignt.h ;
	   case *    : File config.h : configunix.h ;
	}
	LOCATE on config.h = $(LOCATE_SOURCE) ;
This creates a config.h file from either confignt.h or configunix.h, depending on the current build platform.
The File rule does not use the LOCATE_SOURCE variable set by the SubDir rule (although it does use SEARCH_SOURCE), which means you have to set the copied file's output directory yourself. That's done by setting the special LOCATE variable on the target, as shown above, or with the MakeLocate rule described below.

### Bulk Rule

The Bulk rule is a shorthand for many invocations of the File rule when all files are going to the same directory. For example:
	#In Jamrules:
              DISTRIB_GROB = d:\\distrib\\grob ;

	#In Jamfile:
              Bulk $(DISTRIB_GROB) : grobvals.txt grobvars.txt ;
This causes gobvals.txt and grobvars.txt to be copied into the $(DISTRIB_GROB) directory.

### HardLink Rule

The Unix-only HardLink rule makes a hard link (using ln(1)) from the source to the target, if there isn't one already. For example:
              HardLink config.h : configunix.h ;

### Shell Rule

The Shell rule is like the File rule, except that on Unix it makes sure the first line of the target is "#!/bin/sh" and sets the permission to make the file executable. For example:
              Shell /usr/local/bin/add : add.sh ;
You can also use $(SHELLHEADER) to dictate what the first line of the copied file will be. For example:

              Shell /usr/local/bin/add : add.awk ;
              SHELLHEADER on /usr/local/bin/add = "#!/bin/awk -f" ;
This installs an awk(1) script.

### Variables Used When Copying Files

* FILEMODE		Default file permissions for copied files
* SHELLMODE		Default file permissions for Shell rule targets
* MODE		File permissions set on files copied by File, Bulk, and Shell rules. File and Shell sets a target-specific MODE to the current value of $(FILEMODE) or $(SHELLMODE), respectively.
* SHELLHEADER		String to write in first line of Shell targets (default is #!/bin/sh).

## Installing Files

Jambase provides a set of Install* rules to copy files into an destination directory and set permissions on them. On Unix, the install(1) program is used. If the destination directory does not exist, jam creates it first.
All files copied with the Install* rules are dependencies of the install pseudotarget, which means that the command "jam install" will cause the installed copies to be updated. Also, "jam uninstall" will cause the installed copies to be removed.

The Install* rules are:

* InstallBin	Copies file and sets its permission to $(EXEMODE). You must specify the suffixed executable name. E.g.:
* InstallBin $(BINDIR) : thing$(SUFEXE) ;
* 		   
* InstallFile	Copies file and sets its permission to $(FILEMODE). E.g.:
* InstallFile $(DESTDIR) : readme.txt ;
* 		   
* InstallLib	Copies file and sets its permission to $(FILEMODE). You must specify the suffixed library name. E.g.:
* InstallLib $(LIBDIR) : libzoo$(SUFLIB) ;
* 		   
* InstallMan	Copies file into the mann subdirectory of the target directory and sets its permission to $(FILEMODE). E.g., this copies foo.5 into the $(DESTDIR)/man5 directory:
* InstallMan $(DESTDIR) : foo.5 ;
* 		   
* InstallShell	Copies file and sets its permission to $(SHELLMODE). E.g.:
* InstallShell $(DESTDIR) : startup ;
		   
### Variables

The following variables control the installation rules:

* INSTALL		The install program (Unix only)
* FILEMODE		Default file permissions on readable files.
* EXEMODE		Default file permission executable files.
* SHELLMODE		Default file permission on shell script files.
* MODE		Target-specific file permissions

The Install rules set a target-specific MODE to the current value of $(FILEMODE), $(EXEMODE), or $(SHELLMODE), depending on which Install rule was invoked.

The directory variables are just defined for convenience: they must be passed as the target to the appropriate Install rule. The $(INSTALL) and mode variables must be set (globally) before calling the Install rules in order to take effect.

## Miscellaneous Rules

### Clean Rule

The Clean rule defines files to be removed when you run "jam clean". Any site-specific build rules defined in your Jamrules should invoke Clean so that outputs can be removed. E.g.,

	rule ResourceCompiler
	{
	   DEPENDS $(<) : $(>) ;
	   Clean clean : $(<) ;
	}
Most Jambase rules invoke the Clean rule on their built targets, so "jam clean" will remove all compiled objects, libraries, executables, etc.

### MakeLocate Rule

MakeLocate is a single convenient rule that creates a directory, sets LOCATE on a target to that directory, and makes the directory a dependency of the target. It is used by many Jambase rules, and can be invoked directly, e.g.:
		GenFile data.tbl : hxtract data.h ;
		MakeLocate data.tbl : $(TABLEDIR) ;
      
In this example, the File rule creates data.tbl from data.h. The MakeLocate causes data.tbl to be written into the $(TABLEDIR) directory; and if the directory doesn't exist, it is created first.
The MakeLocate rule invokes another Jambase rule, MkDir, to (recursively) create directories. MkDir uses the $(MKDIR) variable to determine the platform-specific command that creates directories.

### RmTemps Rule

Some intermediate files are meant to be temporary. The RmTemps rule can be used to cause jam to delete them after they are used.
RmTemps must be:

the last rule invoked on the permanent file that uses the temporary file(s)
invoked with the permanent file as the output target and the temporary file(s) as the input target
invoked with the exact target identifiers of the permanent file and the temporary file(s)
For example:
		SubDir TOP src big ;
		GenFile big.y : joinfiles part1.y part2.y part3.y ;
		Main bigworld : main.c big.y ;
		RmTemps bigworld$(SUFEXE) : <src!big>big.y ;
	
This causes big.y to be deleted after it has been used to create the bigworld executable. The exact target identifier of big.y is <src!big>big.y (the GenFile and Main rules tack on the grist automatically); the exact target identifier of the bigworld executable is bigworld$(SUFEXE).

## CopyRight

Copyright 1997, 2000 Perforce Software, Inc. 

Comments to info@perforce.com 

Last updated: Dec 31, 2000 
