# 第一课

对于那些已经阅读了 Learning to Program with Haiku 的人来说，我们接下来将要接触到一些内容，它们将帮助我们完成 C++ 基础的学习。那么我们在本节中所要讲解的主题是模板和标准模板库（STL）。对于这两者的学习，需要掌握很多的信息，因此我们需要稳扎稳打的进行。

## 模板

模板是一种归纳抽象用于处理任何类型数据的类或者函数的方式。它们大多数用于创建容器类，例如，堆栈，列表或者其他。但是，如果希望很好的利用这种灵活性的优势，可能需要对使用它们的类或者函数的声明做一些小的修改。一个基于模板的数组类应该如下所示：

    template <class T> 
    class ObjectArray 
    { 
    public: 
    				ObjectArray(const uint32 &size); 
    				ObjectArray(const ObjectArray &from); 
    				~ObjectArray(void); 
     
    	ObjectArray &		operator=(const ObjectArray &from); 
    	T &			operator[](int index); 
     
     	uint32			GetSize(void); 
    private: 
    	T 			*fData; 
     	uint32			fSize; 
    };

在这个实例中，在它和常规的类的定义之间的主要不同就是定义之前的 `template <class T> `板块。该板块告诉编译器，任何时候，我们看到T，我们所指的就是该类中所使用的类型。需要注意的是，我们可以使用任何的名字来命名类型名，例如，T，Key，Type，或者其他我们喜欢的名字，但是我们必须使用在 template 板块中声明的关联于类型名的名字。ObjectArray 类具有两种属性：保存于 fsize 的元素的数量和由 fData 指向的用于存储单个元素的堆中分配缓冲区。一个 ObjectArray 实例可以如下所示进行使用，而不必担心它的实现：

    #include <stdio.h> 
    #include "ObjectArray.h" 
     
    int 
    main(void) 
    { 
    	ObjectArray<int32> intArray(10); 
     
    	for (uint32 i = 0; i < intArray.GetSize(); i++) 
    		intArray[i] = intArray.GetSize() - i; 
     
    	for (uint32 i = 0; i < intArray.GetSize(); i++) 
    		printf("intArray[%ld] is %lu\n", i, intArray[i]); 
    }

使用类模板的关键是指定尖括号中的类型。在上述示例中，我们创建了一个使用 int32 整型作为类型的 ObjectArray 实例。它用于处理所有与 int32 对象相关的工作，而不关心任何其他类型。一旦创建，它就无法改变类型——一个 `ObjectArray<int32>` 不能够修改为 `ObjectArray<float>` 或者 `ObjectArray<int16>`，例如，编译器将把不同类型的 ObjectArray 对象视为完全不同的类型：`ObjectArray<float>` 和`ObjectArray<int64>`，虽然它们都使用 ObjectArray 类作为模板，但是它们不是同一类型。

在一个基于模板的类中定义一个函数不是很困难。许多关联于类名的内容需要包含类型名。下面是用于在上述的类定义中为 ObjectArray 类定义所有函数的代码：

    template <class T> 
    ObjectArray<T>::ObjectArray(const uint32 &size) 
    	:	fData(NULL), 
    		fSize(size) 
    { 
    	if (fSize > 0) 
    		fData = new T[fSize]; 
    }
     
    template <class T> 
    ObjectArray<T>::ObjectArray(const ObjectArray &from) 
    	:	fData(NULL), 
    		fSize(from.GetSize()) 
    { 
    	if (fSize > 0) 
    	{ 
    		fData = new T[fSize]; 
    		for (uint32 i = 0; i < fSize; i++) 
    			fData[i] = from[i]; 
       	} 
    }
     
    template <class T> 
    ObjectArray<T>::~ObjectArray(void) 
    { 
    	delete [] fData; 
    }
     
    template <class T> 
    ObjectArray<T> & 
    ObjectArray<T>::operator=(const ObjectArray &from) 
    { 
    	if (this == &from) 
    		return *this; 
     
    	delete [] fData; 
     
    	fSize = from.GetSize(); 
    	fData = new T[fSize]; 
    	for (uint32 i = 0; i < fSize; i++) 
    		fData[i] = from[i]; 
    }
     
    template <class T> 
    T & 
    ObjectArray<T>::operator[](int index) 
    { 
    	return fData[index]; 
    } 
     
    template <class T> 
    uint32 
    ObjectArray<T>::GetSize(void) 
    { 
    	return fSize; 
    }

除了需要注意在某些地方添加和在每个函数定义头部需要附加类型之外，在使用类模板时，还需要注意一个关键的问题：基于模板的函数定义需要放置在头部而不是主源文件中。这是因为它们是用于函数定义的模板，而不是它们自身的定义。实际的定义在编译时候才会创建。如果这些函数放置在主源文件中，链接器将会返回许多 undefined reference 错误，无需多说，它们不仅仅让人有点疑惑。

有些时候，您不需要让整个类使用同一个模板。可能我们所需要的仅仅是一个或者两个需要使用模板的函数。在下面的实例中，所需要做的仅仅是把模板声明放置在函数声明的返回类型之前，如下所示：

    class MyClass
    {
    				MyClass(void);
    				~MyClass(void);
    			void	SomeRegularFunction(int value);
    template<class T>	void	SomeTemplateFunction(T item); 
    };

在本示例中，SomeTemplateFunction()是 MyClass 中唯一使用了模板的部分。类似于我们的 ObjectArray 类中的函数，它需要在头文件中定义，而不和 MyClass 的其他函数一样处于主源文件中。在下面的实例中，只有函数模板需要放置在头文件中——“常规”的函数应该和通常一样放在独立的源文件中。

## 使用模板：标准模板库（STL）

模板最合适的使用是用于数据容器。幸运的是，对于我们来说，很多人已经花费时间和精力创建了整个基于模板的容器库，称为标准模板库，简称 STL。大部分 Haiku API 都是精心设计的，因此我们不需要经常地使用 STL，但是它确实包含一些很实用的容器。由于 STL 中的容器设计非常精良，因此多数时候，这些模板的使用将被现定于使用这些容器，而不是创建使用这些模板的类。

任何使用了 STL 的 Haiku 项目需要添加一个额外的库。Haiku 的 GCC2 编译需要使用 libstdc++.r4.so 库，而 Haiku 的 GCC4 编译为了使用 STL，则需要链接到 libstdc++.so。

由 STL 提供的容器可以归为两类：顺序容器和关联容器。顺序容器用于处理列表中的元素，它具有一个确定的顺序，例如列表。由 Haiku 的 API 提供的 BList 类也是顺序容器的一个例子。这些容器主要用于处理整个列表，每次一个元素。关联容器主要用于处理那些需要随机或者非整型值查询的数据。可能需要关联容器的最常用的例子是使用字符串查询数据。对于特定容器的选择取决于您处理它们的工作方式。

### 向量

头文件：`<vector>`
vector类非常类似于数组，除了它们的内存分配时自动处理的。向量非常擅长快速的通过索引获取元素，以任何顺序迭代元素，以及在结尾添加和删除元素。但是只有当它用完了内部存储器，并且需要分配更多空间时，添加元素可能会比较慢。

### 双队列

头文件：`<deque>`
双队列（deque）通常发音为“deck”，是双向队列（double-end queue）的简称。双队列非常类似于容器，除了它们可以在首尾两端添加元素，并且它们的元素不需要占用连续的大块内存空间。它有自己的优缺点。使用指针来获取队列中的元素不安全，和向量不相同，但是它们是存储大量元素时较好的选择。

### 列表

头文件：`<list>`
列表被描述为动态分配元素的集合。它们非常适用于处理随意插入，删除，和移动元素等的工作。使用列表主要的缺陷是，无法快速的获取任意的元素——获取列表中的第十个元素需要从开始（或者其他的起始点）迭代至该元素。

## 命名空间

使用 STL 容器需要一些额外的输入，因为它们都被封装在各自的命名空间中。命名空间是创建一组类，函数，数据类型的一种方法。它们通常用于防止不同库中的同名类引起的冲突。一个命名空间如下进行声明：

    namespace myNamespace
    {
    	int32 foo, bar;
    }

获取命名空间中的元素需要指明其命名空间。例如，所有的 SIL 容器都在 std 命名空间中。声明一个 int32 对象的向量如下所示：

    // Note that there is no '.h' for this header and others in the STL. It's
    // just <vector>.
    #include <vector>
    std::vector<int32> intVector;

双冒号是域操作符。指明同一命名空间中的元素不需要使用它。同样的，在全局命名空间中指定一个元素也需要双冒号。

    #include <stdio.h>
    bool gSomeFlag = true;
    namespace myNamespace
    {
    	int intValue = 1;
    	void
    	SomeFunction(void)
    	{
    		// This specifies the gSomeFlag which is in the default (global)
    		// namespace
    		if (::gSomeFlag)
    			printf("myValue is %d\n", intValue);
    	}
    } // end myNamespace

使用其他命名空间中的许多元素可能需要很多的输入工作，因此如果您需要多次使用一个命名空间，您可以使用 using 关键字来消除额外的输入工作。

    #include <deque> 
    #include <stdio.h> 
    // This eliminates the need to add the std:: before each reference 
    // to deque containers. 
    using std::deque; 
     
    int 
    main(void) 
    { 
    	// Declare our deque to accept integers. Without the using statement
    	// above, this would read std::deque<int> myDeque. Unless you're	
    	// using a lot of these, the using statement isn't really needed.
    	deque<int> myDeque; 
     
    	// Add one element to the end of the list which has a value of 5 
    	myDeque.push_back(5); 
     
    	// Print the number of elements in the deque. In this case, we're 
    	// definitely not playing with a full deque. 
    	printf("This deque has %d elements\n", myDeque.size()); 
     
    	return 0; 
    }

using 关键字提供了精细的控制，它们需要我们输入命名空间。在上述示例中，我们消减了使用队列容器时指定命名空间的需要。如果我们同时需要使用向量容器，我们仍然需要键入用于指定 vector 类型调用的 `std::vector<myType>`。如果不存在名字冲突的可能，并且我们需要使用STL中的许多不同类型容器，那么我们可以使用覆盖所有 STL 命名空间的 using 声明来概括所有：

    using namespace std;

在以这种方式使用 using 关键字时需要特别注意。如果您的代码需要处理多个命名空间，非常建议您在每个类的底部使用它，否则，不使用它。然而，如果您正在编写一个调用API 和一些 STL 的 Haiku 程序，那么使用 using 声明将会加快您的工作，而且将会非常有意义。

## STL迭代器

STL 中容器的设计师非常明智的试图使所有容器的 API 尽可能的相似。他们处理这个问题的方式之一是创建迭代器。由于不是所有的容器都像数组一样使用整型来获取其中的元素，因此使用了迭代器。

每个迭代器都差不多是一个容器使用的指向元素类型的指针。在容器中，++ 和 -- 操作符都被重载用于指向下一个或者上一个元素。在 for 循环中使用一个 `vector<int>` 将会如下所示：

    #include <stdio.h> 
    #include <vector> 
    // This using statement only works for the vector class. It also makes
    // the loop code a little more readable.
    using std::vector; 
     
    int 
    main(void) 
    { 
    	vector<int> myVector; 
    	// Add a few values to our vector 
    	myVector.push_back(5); 
    	myVector.push_back(10); 
    	myVector.push_back(15); 
    	// The begin() method will always point to the first item in the
    	// container. end() will always point to the last one. This format
    	// works for *all* STL containers.
    	for (vector<int>::iterator i = myVector.begin(); i != myVector.end(); i++) 
    	{
    	printf("myVector: %d\n", *i); 
    	}
    }

我们已经介绍了许多方法，但是没有给出任何的解释。但是不幸的是，很多时候在现实世界中，对于代码的可用文档就是代码本身，但是在这里我们不需要以这种方式。下面的方法是我们在向量，双列表，和列表以及一些其他有用的东西时曾遇到过的。然而，无论如何，下面是一个解释详尽的列表：

<table border="1">
<tr> <th>方法</th>                       <th>描述</th> </tr>
<tr> <td>iterator begin();</td>          <td>返回指向容器中第一个元素的迭代器。   </td> </tr>
<tr> <td>iterator end();</td>            <td>返回指向容器中最后一个元素的迭代器。</td> </tr>
<tr> <td>size_type size();</td>          <td>返回容器中元素的数目。size_type是一个无符号整型。</td> </tr>
<tr> <td>void push_back(const T &val)</td> <td>添加值为val的元素到容器列表的结尾。需要注意的是，它将会使任何已经存在的迭代器无效。</td> </tr>
<tr> <td>void push_front(const T &val)</td> <td>删除容器中的最后一个元素。</td> </tr>
<tr> <td>void pop_front(); </td>         <td>添加值为val的元素到容器列表的起始。需要注意的是，它将会使任何已经存在的迭代器无效。该方法对于向量类（vector）不可用</td> </tr>
<tr> <td>void clear(); </td>             <td>删除容器中的所有元素。</td> </tr>
</table> 

## 组合使用：使用示例

在看到这些容器之后，我们发现没有许多实例，可以使 BList 看起来更加的有利。例如，使用了这些容器之一的实例可能会比一个标准的发行更加有效率。BList 从一个目录中创建一个 entry_ref 对象的列表。下面是一个示例，它展示了如何可以使我们看到的关于 STL 容器的所有信息应用到更加有意义的地方。

    #include <deque> 
    #include <Directory.h> 
    #include <Entry.h> 
    #include <FindDirectory.h> 
    #include <Path.h> 
    #include <stdio.h> 
    using std::deque; 
     
    int 
    main(void) 
    { 
     
    	// Look up the home folder -- this is much preferable to a
    	// hard-coded way of accessing a system path.
    	BPath path; 
    	find_directory(B_USER_DIRECTORY, &path); 
    	BDirectory dir(path.Path()); 
     
    	deque<entry_ref> refDeque; 
     
    	entry_ref ref; 
    	while (dir.GetNextRef(&ref) == B_OK) 
    	refDeque.push_back(ref); 
     
    	printf("Contents of the home folder: %s\n", path.Path()); 
    	for (deque<entry_ref>::iterator i = refDeque.begin();
    	i != refDeque.end(); i++)
    	{
    	// Note that because an iterator can be treated like
    	// a pointer, we can access each entry_ref's name
    	// using the iterator. 
    	printf("\t%s\n", i->name); 
    	}
    }

## 深入了解

如何修改示例为使用列表而不是双队列？