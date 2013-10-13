# BeOS bash教程

备注：文章来源 - 

## 简介

我认为，bash 和其他的命令行 shell 都被给予了不公平的评价。我经常使用 bash，而且我也喜欢它，所以我认为我应该分享我的使用经验，同时尽量的展示它的用途和优点。

本文的目的在于概述 bash 的主要功能，和一些 BeOS 相关的细节。更多内容，我建议阅读 bash 操作手册，或者其他关于 bash 的书籍。

## 历史

最初，UNIX 中使用的 shell 是 sh，它代表着 shell。bash 是 sh 的 GNU 项目替代品，并且作了扩展。bash 的命名源于 Bourne shell，它是 Steve Bourne 编写的 sh 版本，而且 bash 也是在其基础之上开发的。因此它被命名为 Bourne-again shell（很有趣!）。bash 由 Brian Fox（主要作者）和 Chet Ramey 编写。

## bash 和其他程序

bash 本身用处不大，它本质上是一个交互的编程语言，类似于 C64 上的 BASIC，本身则不能够做任何事情。但是它利用其它的程序来完成所有的工作，类似于调用一个函数，传递参数给该程序，同时把结果传递给其他的程序等。bash唯一需要注意的问题是提供基本的程序原语，以及一个用于定位程序和控制输入输出的的环境。

bash 可以自身可以执行的操作主要限定于处理bash创建的环境如，变量、键位绑定等，处理输入的转换，以及运行内建的命令。bash 的真正的力量在于以命令的形式运行独立的程序，而且使它们看起来像是 bash 的内建功能。

从 GUI 或命令行运行同一个程序之间没有差别。但是当从 GUI 运行时，一些程序不是非常的有用，因此我们可以认为它们是命令行专属程序。

从 bash 运行一个 GUI 程序与双击程序图标运行程序所产生的效果是一样的。给予程序一个参数可以认为是拖拽一些文件至程序的图标。

## 快捷键

在我们开始学习命令之前，了解一些能够简化操作的快捷键将会非常有用。"C"代表 `ctrl`，因此 `C-x` 表示同时按下 `ctrl` 加 `x` 组合键。

* C-c
    退出当前程序。我们不是必须要使用该命令，因为每个程序结束之后都会自动的退出，但是如果一个程序发生了崩溃或者无法退出，我们需要使用该命令强制退出程序
	
* C-d
    发送一个EOF（文件结束标志）字符。该字符位于文件的结尾。如果一个程序以文件作为输入，我们有时可以手动的进行输入，然后我们以该字符作为结束标志。
	
* 上/下箭头

    浏览历史命令

* 左/右箭头

    在当前行之中移动光标。
	
* Tab 键

    将会自动补全当前的输入直到遇到了含混的地方。它同时作用于命令和文件名。如果再次按下Tab 键，那么将会给出所有可能匹配的结果。

## 特殊字符

bash 使用许多特殊的字符 "操作符" 来变换表达式。根据位置的不同，特殊的字符可能无法显示出它的特殊含义，但是我们可以假定所有的特殊字符（例如，`! " # × % & \ () []{}`，还包括空格和回车字符等等）是保留的，不可以用于文本用途。但是我们可以在文件命中使用 "_"，"." 和 "-"，因此我们至少可以猜测出这些字符在文件名中并没有特殊的对待（需要注意的是，"." 字符在文件名中单独使用，而没有与其他字符组合使用时，则是特殊对待的）。

在本节中，我们将会遇到经常用到的特殊字符，因此将它们所有的含义列出来并没有很大的用处。但是在这里，有三个具有通常含义的字符将会非常有用。

* `\`字符称为转义字符，这也就意味着，它将会移除后一个字符的特殊含义。例如，如果我们希望显式的使用空格键，可能在文件名中，我们可以输入"\ "。
* `'` 字符称为单引号。它需要成对使用，而且通常用于自然语言。它保留单引号中所有字符的字面意思。例如，我们再次显示的使用空格字符。词组 "two words" 可以通过引用使其成为一个词组："'two words'"。
* `"` 字符成为双引号，其作用类似于 `'` 字符，只是它不保留 `$`，`'`，`\`，的字面意思。

## 运行命令

为了运行一个命令，我们只需要输入该程序的文件名，或者内建的命令名。我们不需要写出希望运行程序的完整的路径，bash包含了许多的搜索路径，并且假定我们写入了完整的路径。

### ; 操作符

如果希望在同一行中运行多个命令，我们可以使用 `;` 操作符作为分隔符。例如：`comand1 ; comand2`，首先运行 `command1`，其运行结束之后，将会运行 `command2`，该命令完成之后，返回系统控制。

### & 操作符

如果希望在后台运行程序，我们可以在命令之后添加 `&` 操作符。该操作符将会立刻将系统控制返回而不需要等到命令运行结束。例如：`comand1 & comand2 &`，它将会运行 `command1`，然后马上运行 `command2`，之后就返回系统控制。

## 流

当一个程序运行后，将会有三个可用流。其中一个用于储存字符或者读取字符。而且流只可以在一端执行操作，类似于在管道传送小球，读取操作在流的一端进行，等待小球的弹出；而写入操作则在另一端进行，填充小球。流类型有以下几种：

* 标准输入（standard input）
* 标准输出（standard output）
* 标准错误（standard error）

从名字，我们可以看出，它们分别用于数据的输入，输出和错误消息的反馈。输入流仅可以读取，而输出和错误流则只可以执行写入操作。

默认的，输出和错误流将连接到终端程序。这也就意味着，任何打印到他们的内容将会打印到终端窗口，允许 bash 和用户进行交互。当然也并不总是这样，它们也可以进行重定向，例如定向到文件。

### pwd

我们首先遇到的数据流是输出流。我们通常感兴趣的是如何获取信息，因此输出是非常重要的。我们首次启动一个终端（并且是 bash），首先看到的是一个 `$` 标志（提示符），其中并没有太多的提示内容。我们可能对文件的操作比较感兴趣，因此知道我们在当前文件系统中所处的位置很有用。而我们可以使用 pwd 命令来完成，它用于显示当前的工作目录。当我们输入该命令之后，bash 就认为我们希望运行它，而执行相关的操作。该命令将会计算出我们所处的位置，然后将其打印到输出流，例如 `/boot/home` 。当命令运行结束之后，bash 将会返回控制，再次显示命令提示符。

### ls

如果希望检查邻近的文件，或者列出目录下的内容，我们需要运行 ls 命令（表示 list）。只要输入 ls 命令，就可以显示当前目录中的文件。如果以一个文件作为参数，那么将会列出该文件，如果是一个目录，那么将会列出该目录中下的内容。我们也可以给出ls的任意参数值，它将会以特定的规则列出所有的文件。默认的，ls 将会打印出文件的文件名。接下来我们将会了解如何使用ls获取文件的更多信息。

我们可以使用无效的参数运行 ls 命令来演示错误流，例如使用不存在的文件。ls 将会打印错误消息到错误流。然而对我们来说，错误行为和错误流在这里是无法分辨的。

### cat

为了查看某个文件的内容，我们可以使用 cat 程序。它将会依次打印出参数的内容（它必须是文件），因此，它可以连接文件。

## < 操作符

现在我们可以介绍输入流。除了可以把文件作为参数传递给 cat，我们也可以把文件的内容插入到 cat 输入流，然后 cat 将会将其打印到输出流。我们可以使用 `<` 操作符来执行该操作，如：`cat < myfile`。空格键不是必须的，我们也可以输入：`cat&ltmyfile`，但是添加空格可以提高可读性。我们可以看到不附带任何参数运行 cat 将会打印出任何输入的内容。如果我们在之后输入了其他内容，它将会再次打印出它们。

### less

如果我们使用 cat 来查看大型文件，我们将只能看到该文件的最后几行，因为其它的内容将会很快的滚动而过，或者需要使用滚动条来滚动浏览文件内容。而使用 less 来浏览文件夹爱你更会是比较好的方式。最初，有一个程序 more，它可以用于此。当它显示了一页的文本之后，将会暂停向后显示，然后显示一个提示 `--More--`，然后依据按键继续。less 也以同样的方式工作，只是它可以执行回放操作，并因此命名。它同时还具有搜索，以及其它的特性。和 cat 相似，less 可以从输入流中接受输入。为了找出 less 使用了那些按键，我们只需要在 less 会话之后输入 `h` 即可。

### echo

为了创建我们的输出，我们可以使用echo程序。Echo只简单的以我们输入的方式输出我们给予它的参数。

## 标记

许多程序可以以 标记 作为参数，而且依据标记的不同来完成有用的任务。标记并没有什么特别的地方，它们只是通常的参数，但是有一个约定，以短横线（`-`）开头的参数成为标记。例如，多数的程序都接受 `-h` 标记作为参数，而且将会显示出帮助文档。同时，您可以使用 `--help`。这里似乎还有一个约定，那就是使用双横线和完整的词组和，而单横线同单个字符组合。

### ls（继续）

直到目前，ls 是唯一使用标记非常广泛的程序。有许多标记可以控制 ls 显示的文件或者目录的信息。`ls -l`（长格式）将会显示文件的大小，修改时间，保护位以及所属用户。由于目录本质上也是文件，我们也可以对目录执行同样的操作。但是，如果我们确定某个目录时，ls 假定我们希望查看目录下的内容。因此我们如果希望获取目录的长格式信息，我们可以输入：`ls -ld mydirectory`。还有一个非常有用的标记是 `-R` ，它将会递归的列出一个目录，例如，它将会列出目录，以及其下所有的子目录。还有许多其它的标记，但是这里所介绍的我们将会经常用到。

## 重定向

### > 操作符

我们已经了解了如何使用 <操作符 把文件输入到程序的输入流。那么我们也可以执行相反的操作，重定向程序的输出流到文件。这可以通过 `>` 操作符 来完成，例如：`cat myfile > myotherfile`。我们也可以对两个操作符进行组合使用：`cat < myfile > myotherfile`。该组合命令将会同前面的例子产生同样的结果。还有一个关于 ls 的非常有用的例子。如果 ls 产生的输出超除了屏幕的限度，我们将会错过顶部的某些内容。下面就是一个补救的措施：`ls > filelisting`，然后运行：`less filelisting`。

重定向不仅适用于输出流，而且适用于程序任何向外输出的流（例如，多数用于实用目的的输出流和错误流）。单独使用 `>` 操作符 将会隐式的解释为 `1>` 。这也就意味着，输出流的数值为 `1`。因此，错误流的数值为 `2`，并且重定向输出流通过 `2>` 完成。我们可以使用 ls 进行演示。文件 foo 不存在，因此输入 `ls foo` 将会在错误流中创建一个消息。如果我们输入：`ls foo > errorfile`，或者`ls foo 1> errorfile`，我们仍然可以看到错误信息，而且 errorfile 文件将会保持为空。但是，如果我们输入：`ls foo 2> errorfile`，我们将不会看到错误消息，因为它已经重定向到了 errorfile 文件。

### >> 操作符

当我们使用 `n> `操作符（n 可以是任何数值），初始时用于输出的文件将为空，而不论它之前是否包含任何内容，换句话说，它将会被清空。如果我们希望添加输出流到某个文件，我们可以使用 `>>` 操作符 。它与 `>` 操作符 工作方式完全相同，而且还包括接受的参数值。

### << 操作符

为了对称性的缘故，我们也可以尝试一下 `<<` 操作符 ，或者 `here`  命令。它可以认为是一个原始的文本行编辑器。它以一个词作为参数，而且它将会读取行，直到在某行中遇到了同一个词，然后它将会把该文本填充到位于该操作符之前的程序的输入流。

### | 操作符

Bash 最强大的特性可能就是管道操作符，`|` 。它可以连接其左端程序的输出流到其右端程序的输入流。它为上述的 ls 程序提供了非常优雅的解决方法：`ls | less`。我们也可以为管道添加首个管道操作符产生的任何最终输出，例如：`ls | cat | less`（一个毫无用处的例子）。这种方法可以用于组合许多小的程序，对于单个程序来讲可能只能够完成输入流较小的转换，但是组合起来则变得非常强大。而且，所有管道连接的程序可以同时运行，因此当第一个程序产生输出后，马上后一个程序就开始对结果进行处理。

## 文件工具

下面将要介绍的是我们需要的基本文件工具。需要注意的是，所有可能造成破坏性结果的工具（例如，删除，易懂，覆盖操作等）在执行操作之前没有任何的警告，而且也无法撤消该操作。

### cd

我们可以使用 cd 来进行文件系统的导航，它表示的意思是 change directory。为了决定我们将要进入的位置，cd 需要一个新的目录作为参数。该参数可以是当前位置的相对位置，例如：`cd config` will take us to `/boot/home/config` if we are in `/boot/home`, or it can be absolute, for example: `cd /boot/home/config` will take us to `/boot/home/config` regardless of where we were in the filesystem.

In every directory, there will also be two virtual directories:

* `.`
    The current directory.
* `..`
    The parent directory.

### touch

To create a new file, we can use the touch program. It will create a file with the filename specified by the argument. If the file already exists, it will touch it (hence the name), ie. it will change the modification time of the file, to make it appear as if the file has been edited. Giving more than one argument will simply touch all the specified files.

### cp

Copying files can be done with the program cp (for copy). If given two filenames as arguments, cp will copy the first one to the second. We can also give a whole list as arguments, and a directory as the last argument. Doing this will copy the files in the list to the directory. Similarly to ls, we can copy directories recursively. In this case we specify the -r flag.

### mv

mv (for move) works like cp, just that it moves files instead of copying. Moving a directory will also automatically be done recursively, or rather, it will rename the directory.

### rm

rm removes the files specified as arguments. It also can remove recursively, by specifying the -r flag.

### mkdir

mkdir (make directory) creates directories with names specified by the arguments.

### rmdir

rmdir (remove directory) removes directories.

## Expansion

### Pathname Expansion

Pathname expansion can be said to be a kind of pattern matching. It will expand to all the files which match the pattern. It uses three special characters:

* `*`
    Matches any string, including the empty string. "foo*" will match for example "foo" and "foobar", "*.jpg" will match any files with the extension ".jpg".
* `?`
    Matches any single character. "foo?" will match for example "fooa" and "foob", but not "foo" alone, or "fooab".
* `[...]`
    This is similar to how regular expressions work. It will match any of the enclosed characters, or any of the characters in a range, specified by a dash between two characters. It will match any character not enclosed by negating the expression with a ! or ^ as first character in the enclosure. "foo[abc]" will match for example "fooa", "foob" and "fooc". "foo[^a-c]" will match for example "food" and "fool", but not "fooa", "foob" or "fooc".

### Arithmetic Expansion

Arithmetic expansion will evaluate an arithmetic expression and replace the expression with the result. The syntax for performing arithmetic expansion is like so: $(( expression )). The expression syntax is very much like the C syntax for arithmetic expressions, see the manual page for details. For example, "echo $(( 2 + 3 * 5 ))" would print "17".

### Command Substitution

Command substitution is another extremely powerful feature of bash. It replaces the command with its output. The syntax for command substitution is like so: $( command ). For example, (BeOS specific) "cp $( query -a 'name=foo' )" will copy all files named "foo" in the filesystem to the current directory.

Another example is to use the program bc to replace arithmetic expansion. bc is a calculator program, which can take expressions on the input stream, and output the results. The equivalent of our example of arithmetic expansion using command substitution and bc would be: "echo $( echo '2 + 3 * 5' | bc )", which would print "17".

## Parameters (or Variables)

Parameters are similar to variables in most programming languages. A parameter is set by "name=value", and the values of the parameters can be viewed with the "set" command ("set|less" might be better). To retrieve the value of a parameter, we write "$name". A useful feature of parameters is that the value need not expanded until it is read, ie. the values can be set to text, which is not expanded until retrieved. To do so, we can wrap the value with single quotes when assigning.

bash defines both some "special" parameters which are somewhat magic, and also some parameters which are "normal", but which bash uses for different purposes and which can be modified. For example, a magic parameter is RANDOM, which will return a random integer between 0 and 32768.

### Prompting and PS1

A normal parameter which is very useful, is PS1. This is the prompt bash displays when it's waiting for input. By default it's set to "$ ". If we want the current directory to be displayed in the prompt, we can set PS1 to "'$( pwd )$ '" (note the single quotes, also note that the second $ is interpreted as a literal). This will execute "pwd" every time the prompt is shown and show the output in the prompt.

Note that prompting is also slightly magical, and uses some special characters. See the manual page for more details. It might be more intelligent to use these in the prompt instead. For example, to achieve the same result as above, using these, we can write "PS1='\w$ '".

### PATH

Another quite important parameter is the PATH parameter. It contains all the paths bash will search for a given command, separated by the : character. To add a path to it, we can write: "PATH=$PATH:new_path". It should include /bin, /boot/home/config/bin and . (current directory) by default.

### export

It's not only bash which has these kind of parameters. In fact, every program has a set of parameters, regardless if it has been started from bash or not. Some programs, like bash, also make use of these for different purposes. For example, under BeOS every program will honor a parameter MALLOC_DEBUG, which will affect how programs handle memory, with regards to debugging.

Simply setting a parameter in bash won't set it for all programs. Instead, we can use export &ltname>=&ltvalue>, which will make all subsequent programs executed from the bash session inherit the parameter and its value.

## Return Values

Aside from any eventual output on the output stream a program produces, every program will return an integer value, regardless of whether it wants to or not. This value is not directly accessible to us, but we can use it indirectly through some bash operators.

Most of the time, we will use the output stream to return things with. However, to indicate if a program encountered an error, it's more useful to use return values. There exists a convention that a program should return 0 on success, and some other value on error. Some programs do not adhere to this, but most command-line tools should.

There are two operators which leverage this:

* `command1 && command2`
    Execute the second command if, and only if, the first command returns a success, ie. 0.
* `command1 || command2`
    Execute the second command if, and only if, the first command returns a failure, ie. not 0.
	
There is also another operator which deals with the return value of programs:
* `! command`
    Boolean NOT. Negates the return value of the command.
	
Return values are perhaps mostly used for flow of execution, as will be described more below.

## Programming Primitives

bash supports several programming primitives shared by most programming languages. It can perform choices (if then else, case), it can loop (for,while, until) and it has functions (function).

### if and test, or [ ]

The if construct, slightly simplified, looks like this: if list1 then list2 else list3 fi, where a list is a sequence of commands separated by, and terminated by a ; or newline. The return value of such a list is the return value of the last command in the sequence. Optionally, we can wrap the list in curly brackets: { list }.

if will check the return value of list1. If it's 0, it will execute list2, and if it's non-zero, execute list3.

It's here the test program comes in, also known as `[ ]`. test evaluates an expression and returns 0 or 1 depending on the result. The expression is simply an argument list given to the program. Instead of writing test expression, we can wrap the expression in square braces, like so: `[ expression ]`. Note however that test and `[` is the same program (with slightly different syntax, ie. `[` wants a closing bracket), so this is not some magic bash performs. Note also that any program can be used as a test in the if construct.

Here are some of the valid operators in a test expression:

* `expression1 -a expression2`
    Boolean AND. True if both expressions are true.
* `expression1 -o expression2`
    Boolean OR. True if any expression is true
* `string1 = string2`
    Compares two strings for equality.
* `-e file`
    True if the file exists.
* `-d file`
True if the file exists and is a directory.
Example:

    if [ "foo" = "foo" ] ; then
    ls
    else
    pwd
    fi

### for

for works slightly different than how it is commonly used. Instead of incrementing/decrementing a variable at each loop, it walks a parameter through a list of words, executing the loop for each step. The construct looks like this: for name in words do list done. The same rules for the list apply as in if.

Example:

    for a in 1 2 3 ; do
    touch foo_$a
    done

There usually exists a program seq which allows for in bash to behave similar to what is common. It takes two numbers as arguments, and prints out the sequence of all the numbers ranging between those two. seq allows us to write for loops like this:

Example:

    for a in $( seq 1 10 ) ; do
    touch foo_$a
    done
    This will touch the files: "foo_1" through "foo_10".

However, there doesn't seem to be a seq program distributed with BeOS, so this is not possible. We can implement our own seq though, as an exercise in writing functions below. It will not be the syntactic equivalent to the real program, but it will demonstrate what we want.

### while

while works like if, only it will keep exeuting the first list while the test is true, and break if not. The construct looks like this: while list1 do list2 done.

Example:

    while [ -d mydirectory ] ; do
    ls -l mydirectory >> logfile
    echo -- SEPARATOR -- >> logfile
    sleep 60
    done

This will log the contents of "mydirectory" every minute, as long as the directory still exists. The sleep command is new. It will pause for the number of seconds specified.

### function

#### Syntax

bash functions are quite powerful. A function will behave just like a command, so we can write new commands quite easily. The function construct looks like this: function name () { list }, where the function word is optional, and a list is the same as before. Note that functions allow recursion, so it's allowed to execute the function we are defining inside itself.

#### Arguments

To access the arguments of the function, we use the positional parameters made available to all functions. They are named $n, where n is the number of the argument we wish to access starting from 1, ie. $1 is the first argument. We can also get all arguments at once with $*, and the number of arguments with $#.

#### local

If we want to create a local parameter, we can use the local keyword. The syntax is the same as for defining a normal parameter, just that we precede the definition with local, like so: local name=value.

#### seq

Example:

    seq()
    {
    local I=$1;
    while [ $2 != $I ]; do
    {
    echo -n "$I ";
    I=$(( $I + 1 ))
    };
    done;
    echo $2
    }

This is the implementation of seq as discussed in the for section. Note the "-n" flag passed to echo, it will suppress the printing of a newline. Though this is not necessary for the use we have in mind, it might be for other uses.

#### fact

Example:

    fact()
    {
    if [ $1 = 0 ]; then
    echo 1;
    else
    {
    echo $(( $1 * $( fact $(( $1 - 1 )) ) ))
    };
    fi
    }

This is the factorial function, an example of a recursive function. Note the use of both arithmetic expansion and command substitution.

## Shell Scripts

Similar to how functions work, it's possible to execute a script as a command. A shell script is just a file containing shell commands. The syntax for accessing arguments is the same as for functions.

### source or .

To execute a script inside the current bash session, we can use the source command, also known as ".". It simply takes shell scripts as arguments.

### sh

We can also start a new bash session and pass the script to that to execute it for us. By default on BeOS, bash is named sh, and resides in /bin/sh. So to execute a script, we can write "sh myscript".

If we examine some scripts, we might notice the first line to be `#!/bin/sh`. This means that when we execute the script like a normal command, /bin/sh should execute it for us. We can replace this line to point at any program which can read the file, for example `#!/bin/perl` can be used for perl scripts.

We can also note that the `#` character is a comment character. Every subsequent character on the same line as a `#` will be ignored. To demonstrate this we can write `# ls` and notice how it is ignored.

## Aliases

Instead of using functions, we can use aliases. An alias will essentially replace a given command by some other text.

The syntax for defining an alias is: `alias &ltname>=&lttext>`. To define aliases with multi-word text, we need to wrap the text in quotes.

Aliases and functions may seem to fulfill the same purpose, and they actually do. One difference though is that an alias need not be recursively defined. It's impossible to alias "`ls`" to "`ls -l`" with a function, since it would recurse infinitely. With aliases however, we just write "`alias ls='ls -l'`". Also, alias is probably still around for historical reasons.

## Some Useful Programs

### ps

ps stands for process status, and prints information about the currently running processes (teams under BeOS) and threads on the system. Aside from letting us know which teams and threads are currently running, it will also let us know their ID numbers, which is good to know for the next program for example.

### kill

kill will send a specified signal to a thread or program, usually making it quit. kill takes a thread ID or program name, and optionally a signal type as arguments. Signal types are specified as a dash followed by a number: "kill -9" for example.

It's useful to know two signals, SIGHUP (hangup) and SIGKILL (kill), number 1 and 9 respectively. They both intend to kill the recipient of the signal, but, at least to my knowledge, the difference is that SIGHUP is somehow nicer to the recipient.

For example, instead of rebooting to restart Tracker, we can write: "kill -9 Tracker", and then "/system/Tracker &" to restart it.

Also interesting to note is that pressing C-c actually sends a signal, SIGINT (interrupt), or number 2, to the current program.

### grep

grep (global regular expression processor(?)) works as a filter, printing lines in files matching a pattern.

The pattern is defined using a regular expression, which we have touched earlier. For a thorough description of what a regular expression is, I suggest reading the grep manual page, it also describes all the grep flags.

The basic regular expression is the one matching only a single character. Most characters are their own regular expressions, ie. the regular expression "a" matches the character "a". We can also create a list of characters enclosed by square brackets, which will match any character in the list. The regular expression "`[abc]`" will match the character "a", "b", or "c". Lists also allow ranges, where we specify a range by a start character, a dash, and an end character, like so: "`[a-z]`", which will match any lowercase character from "a" to "z".

To match strings longer than one character, we can concatenate many such expressions. The regular expression "abc" will match the string "abc", and similarly for lists: "`[abc][abc][abc]`" will match any permutation of "abc" ("cab" and "bca" for example).

There are also many operators which control regular expressions. For some reason they need to be escaped, I don't know if this is specific for grep. Two useful operators are `^` and `|`. `^` inside a list will negate that lists meaning, ie. the list will match any character not included in the list. The expression "`[^abc]`" will match any character except "a", "b" or "c". Outside of lists, `^` has another meaning. The operator `|` is similar to Boolean OR. It will match either the expression to the left of it, or the expression to the right. The expression "`foo\|bar`" will match either "foo" or "bar".

For example, to print all the lines containing the word "BeOS" from some files, we can write "`grep BeOS file1 file2 ...`".

As regular expressions often contain special characters, it's wise to enclose the expression in single quotes. To print all the headers in a HTML file, we could write "`grep '<[Hh][0-9]>' file.html`".

grep can also work with the input stream, so "`ls | grep -v '.bak' | less`" will list all the files not having ".bak" in the filename. The flag "`-v`" will print every line not matching the expression.

### browse and TermHire

Two very nice programs for shell/GUI integration are browse and TermHire (none of them included with BeOS) by Kevin Hendrickson and Pierre Brua respectively. They allow for easy switching between bash and Tracker without leaving the current directory, and also some other things.

browse takes files as arguments and will open the files with the appropriate program, for directories that would be Tracker. It can also take URL:s as arguments, and will open those with NetPositive.

TermHire essentially does the opposite of browse, but only for directories. It's a Tracker add-on that will open a bash session (ie. a Terminal) at the directory it was launched from.

## Author

The author of this document is Johan Jansson, d96-jja@nada.kth.se. Please write if you discover any errors or have any questions.