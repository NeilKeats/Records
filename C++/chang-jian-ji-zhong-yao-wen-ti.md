> C++常见重要问题

# 关键字

## const & static

### const 

**const**关键字用来修饰（声明）变量，则表示变量是一个常量，不可修改，所以这个变量也必须初始化。对于一些在程序运行过程中不应该被修改的变量，应该加const予以保护。

**问**：为什么不用marco？

**答**：const变量是一个变量相比marco更安全，因为他是有类型的，在参数传递的时候可以进行类型检查。

对于**类的成员函数**而言，加上了const关键字则表明这个成员函数可以被常量对象调用，也可以被非常量对象调用。如果不加的话则只能被非常量对象调用。【可以用来重载类成员函数，可以实现非常量以及常量版本】。注意，如果要返回对象的引用，那么**常量版本**和**非常量版本**的返回类型不同。

```cpp
class A{
    A& func(){};
    const A& func() const {};
}
```

作为函数参数，顶层const会被忽略

### static

**面向过程的：静态变量/函数**

-   静态**全局/局部**变量的生命周期都是程序的运行期间，存储在全局数据区的内存中，若没有初始值则自动初始化为0。

-   静态**全局**变量只在当前文件可用，文件内都可见这个变量；静态**局部**变量，只在当前语句块内可见（作用域），在首次经过对象定义语句时初始化。

-   静态**函数**：只在当前文件内可用


**面向对象：类的静态成员**

-   依赖于类，不依赖于具体对象，对象中不包含任何与类静态成员有关的数据，可以使用作用域访问：

    -   `<类型名>::<静态数据成员名>`，`<类型名>::<静态函数名>(<参数表>)`
    -   也可以用对象来访问：`<对象名>.<静态数据成员名>`，`<对象名>.<静态函数名>(<参数表>)`（指针同理）

-   访问遵循`public, private, protected`规则

-   在外部**定义**、**初始化**静态成员时，不能重复static关键字，static只能在内类出现。

    -   ```c++
        class Bar{
          static int a;
          static void fff();
        }

        int Bar::a = 1; //定义并初始化,private对象的初始化可以直接进行
        void Bar:::fff(){
          ///...
        };
        ```

-   **类的静态成员变量**

    -   只有一份数据，存储在全局数据区，生命周期是整个程序运行期间。类以及类的所有对象共享。
    -   一般在类外部初始化，也可以在类内初始化（建议在类外也定义），但是必须为字面值常量。

-   **类的静态成员函数**
    -   不包含this指针
    -   在类外定义时，不加static关键字
    -   可以调用访问静态成员（数据、函数），但是不能访问非静态成员
    -   可以被非静态成员访问

-   好处

    -   可以所有对象共享

    -   不进入全局命名空间

    -   可以实现信息隐藏

    -   可以用于特殊场合

        -   静态数据成员可以是不完全类型（如当前类的数据成员），而普通数据成员不能是不完全类型（普通指针成员可以是）	

        -   ```c++
            class Bar{
            public:
            	//...
            private:
            	static Bar mem1;	//合法，静态成员可以是不完全类型
            	Bar *mem2;			//合法，普通指针成员可以是不完全类型
            	Bar mem3;			//不合法，普通成员必须是完全类型
            }
            ```

            ​


参考：[知乎](https://zhuanlan.zhihu.com/p/37439983)

## sizeof



## inline

内联函数在编译过程中会由编译器将内联函数的代码在调用点展开并替换为函数代码，因此在运行时没有实际调用函数。而我们在使用inline关键字的时候，只是建议编译器进行内联。

**好处**：

-   内联函数更快，因为您不需要在堆栈上推送和弹出内容，如参数和返回地址，避免参数拷贝。
-   可重用，减少代码重复，且修改方便。
-   本身是一个函数，会检查参数问题，更安全（可与\#define宏相比）

内联函数应该在头文件中定义，这一点不同于其他函数。编译器在调用点内联展开函数的代码时，必须能够找到 inline 函数的**定义**才能将调用函数替换为函数代码，而对于在头文件中仅有函数声明是不够的。

参考：[stackoverflow:Benefits of inline function in C++ ](https://stackoverflow.com/questions/145838/benefits-of-inline-functions-in-c)，[When use Inline](https://stackoverflow.com/questions/1759300/when-should-i-write-the-keyword-inline-for-a-function-method/1759575#1759575)

---

# 操作符

## ++iter; 与 iter++;



## new与malloc的区别：

**`new`**(new operator)是C++的关键字，而`malloc`是C语言标准库的函数。

**主要区别**：

1.  `malloc`只负责申请空间，并返回一个`void*`指针；而`new`会申请空间，并调用构造函数，返回一个相同类型的指针。
2.  `malloc`在调用时还需指定空间大小。
3.  `malloc`从**堆**申请空间，而`new`从**自由存储区**申请空间（依赖于operator new的实现）
4.  处理数组时，可以使用`new A[n]`这样的语法来申请，而`malloc`需要指定`(A*)malloc(sizeof(A)*n)`
5.  `new`(new operator)不可重载，而operator new可以重载。（注意区别）
6.  `malloc`可以用`realloc`重新分配内存，`new`不行
7.  `new/new[]`与`delete/delete[]`搭配；`malloc`与`free`搭配。

**`new`**的主要功能：

1.  申请内存(operator new, 或者 operator new[])
2.  调用对象构造函数
3.  返回指向对象的指针

默认情况下，`new`动态分配的对象是默认初始化的（内置类型或组合类型的值未定义）类类型则用默认构造函数初始化。

可以用**直接初始化**方式、**传统构造方式**（圆括号）、**列表初始化**（花括号）、也可以进行值**初始化**

```c++
int *pi = new int(1024);						//直接初始化
string *ps = new string(10, '9');				//构造初始化
vector<int> *pv = new vector<int>{0,1,2,3,4};	//列表初始化

string *ps1 = new string;			//默认初始化
string *ps2 = new string();			//值初始化，对类类型等价于默认初始化，调用默认构造函数
int *pi1 = new int;					//默认初始化，*pi1的值未定义
int *pi2 = new int();				//值初始化，*pi2的值为0。对内置类型，值初始化有良好的定义值。
```

**动态分配const对象**

可以动态分配const对象，但必须初始化

```c++
const int *pci =  new const int(1024);	//初始值为1024
const string *pcs = new const string; //空字符串
```

**`delete`**：

1.  调用析构函数
2.  回收内存(operator delete, 或operator delete[])

`delete`可以对空指针安全操作。需要注意的是，`delete`之后应当重置指针。

## 异常情况

`malloc`申请失败返回NULL

`new`失败抛出`bad_alloc`异常，可以使用`new_handler`处理错误，也可以忽略`(nothrow)`。



**参考**：

[细说new与malloc的10点区别  ](https://www.cnblogs.com/QG-whz/p/5140930.html)

[In what cases do I use malloc vs new?      
](https://stackoverflow.com/questions/184537/in-what-cases-do-i-use-malloc-vs-new)

[operator new和operator new[]区别？](https://www.zhihu.com/question/25497587)

[C++之operator new 和new operator区别](https://bbs.huaweicloud.com/blogs/306d689fa42411e7b8317ca23e93a891)



# 类与继承

[多态与虚函数](http://huqunxing.site/2016/09/08/C++%20%E4%B8%89%E5%A4%A7%E7%89%B9%E6%80%A7%E4%B9%8B%E5%A4%9A%E6%80%81/)



## 虚基类，虚继承

[虚基类](https://zh.wikipedia.org/wiki/虚继承)

[虚继承](https://zh.wikipedia.org/wiki/虚继承)

## 虚函数/纯虚函数，虚函数表

[虚函数/纯虚函数/虚函数表](https://zhuanlan.zhihu.com/p/37331092)

[虚函数表](https://blog.csdn.net/lihao21/article/details/50688337)

## 

---

# 面向对象

## 三大特征，五大基本原则



# C++标准库

**C++标准库**，C++ Standard Library，是类库和函数的集合，由c++标准委员会制定，并不断维护更新。标准程序库提供若干泛型容器、函数对象、泛型字符串和流（包含交互和文件I/O），支持部分语言特性和常用的函数，如开平方根。C++标准程序库也吸收了ISO C90 C标准程序库。标准程序库的特性宣告于std名字空间之中。  
std命名空间是C++中标准库类型对象的命名空间  

**STL**（标准模板库，英文：Standard Template Library，缩写：STL），是一个C++软件库，大量影响了C++标准程序库但**并非是其的一部分**。其中包含4个组件，分别为算法、容器、函数、迭代器。实现了基于模版的数据结构和算法。  

**联系**：标准库大量参考了并基于标准模板程序库（STL）所创建的习惯用法，包含容器、算法、迭代器、函数对象等。  

参考：[WIKI：C++标准库](https://zh.wikipedia.org/wiki/C%2B%2B標準函式庫)，[WIKI：STL](https://zh.wikipedia.org/wiki/标准模板库)，[C++标准库/STD/STL等的区别](https://blog.csdn.net/KingCat666/article/details/44995653)

## 智能指针





---

[百度C++提前批](https://www.nowcoder.com/discuss/96139?type=0&order=0&pos=16&page=1)

[C++后台开发校招面试常见问题](https://blog.csdn.net/shanghairuoxiao/article/details/72876248)

