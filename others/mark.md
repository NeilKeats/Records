# 牛客 2.20笔试

**选择题**：

1.  cron后台常驻程序(daemon)用于？`crontab`
    -   定时任务，每分钟检查一次。
2.  最小生成树

**编程题**：

存在问题：编码速度太慢

第二题没想清楚，没写完

第三题最短路径，不会。

# CVTE-C/C++笔试\[2019.3.2\]

## 选择题知识点

* static、const函数：

* 面向对象**依赖倒置**

* STL的atmoic语句，线程安全

* HTTP的REQUEST

## 编程题

* Zigzag遍历矩阵得到的序列，求原矩阵（反序列化）
* [剑指offer，把数组的数排成最小（最大的数）](https://www.nowcoder.com/practice/8fecd3f8ba334add803bf2a06af1b993?tpId=13&tqId=11185&tPage=2&rp=2&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 面试预习\[2019.3.6\]

根据[牛客的C++面经](https://www.nowcoder.com/discuss/158497)：

* 有了解多态吗？

  * > 多态是C++的一种特性，同一个接口可以对不同类型提供不同的实现方法，可以达到接口重用的目的。
    >
    > 在C++里面主要分为两种，一个是静态多态，一个是动态多态。静态多态通过在定义重载函数来实现，在编译时可以确定具体调用的是哪个重载的实现【具体过程为函数匹配，参考/C++/语句与函数】。
    >
    > 对于动态多态，对基类中声明虚函数然后派生类可以定义各自的虚函数实现，通过使用基类指针或引用调用虚函数来达到多态。编译时可以确定指针或者引用的静态类型（基类类型），运行时才能知道指针指向或引用的对象的实际类型，根据类型选择函数（动态绑定）。【引申下一个话题】

* 虚函数的实现，虚函数表指针是类所有还是对象所有，虚函数表呢？

  * > 通过让每个类维护一个虚函数表，里面存储函数指针指向这个类的虚函数地址，虚函数表是类共有的。对象拥有一个虚函数表指针，是在构造函数运行时初始化，指向所属类的虚函数表。
    >
    > 虚函数实现的过程是：**通过对象内存中的虚函数指针vptr找到虚函数表vtbl，再通过vtbl中的函数指针找到对应虚函数的实现区域并进行调用。**所以虚函数的调用时由指针所指向内存块的具体类型决定的。

* auto关键字有用过吗？

  * 可以不指明变量的类型，让编译器自动推断表达式的类型。通常会忽略顶层const，保留底层const
  * 【拓展】decltype：选择并返回表达式的类型，且返回变量类型（包括顶层const，引用）

* 手写二分查找

  * ​

* 结构体内存对齐，给一个结构体让分析

  * [https://zhuanlan.zhihu.com/p/26122273](https://zhuanlan.zhihu.com/p/26122273)

* TCP和UDP的区别？两个进程在使用TCP通信，如何检测对方进程没有挂掉。

  * ​

* MTU是什么

  * 最大传输单元，IP包在传输时需要根据链路上的MTU判断是否需要分片

* C语言的宏定义函数怎么用

  * ```c++
    #define add(a,b) a+b //错误
    #define add(a,b) ((a)+(b)) //正确
    ```

    \#define由预编译器处理，直接执行替换原来的式子，若不加括号遇到更高优先级的操作符会出现意料之外的结果

* 拷贝构造函数的const和引用有什么用？

  * > 拷贝构造函数需要引用
  * > ```c++
    >   Sales_data::Sales_data(const Sales_data &orig){}
    > ```
    >
    > 若不引用，则此处值传递，又会调用拷贝构造函数，如此循环，没法完成拷贝构造
    >
    > ```C++
    >   Sales_data::Sales_data(Sales_data &orig){}
    >
    >   const Sales_data a;
    >   Sales_data b = a; //错误 a是 const类型
    > ```
    >
    > 若不使用const类型，则无法使用常量变量进行拷贝构造。此外由于引用，使用const可以保证不修改引用的对象。

* 什么时候会出现重载

  * > 当调用函数时，在调用点可见的作用域内有多个形参列表不同、名字相同的函数时，会出现函数重载。由编译器负责选择具体的重载版本，根据实参的类型

* STL有用过吗，map用过没有？

  * vector用的多，可能深入展开为vector的增长模式？
  * map待补充

* C ++11有了解哪些

  * 列表初始化
  * 范围for
  * auto关键字
  * nullptr关键字

* 日常最常用的C ++技术有哪些

  * 用的多的是new、stl的vector

* 一个项目里最深刻的一点

* 又问了一下多态和虚函数，虚函数表（这个是中间问的问题）

* C ++11和boost库用过哪些

* STL用过哪些，vector最多可以申请多大内存。

* C ++的异常有哪几种？

* Windows下DLL需要多加哪些语句。

* 让你写一个线程池需要写什么功能。【？】

* vector是线程安全的吗？用什么可以保证vector的线程安全。【？】

* 有哪些实现同步的方式，如何确保线程释放锁。【？】

---

[第二个牛客面经](https://www.nowcoder.com/discuss/22775)

[https://www.nowcoder.com/discuss/22775](https://www.nowcoder.com/discuss/22775)

* C++ 里面的static作用，const作用 
* 写一个接口，刚开始没太懂，后来我写了一个抽象基类带了一个纯虚函数 
* 写一个比较大小的模板函数 
* 写一个单例模式的类 
* 写vector的扩容过程 
* STL 里面的数据结构 
* C++11 里面的智能指针有哪些，区别是什么 
* TCP建立连接过程的具体函数调用

---

## 凉经

* CPU和GPU的区别？
  * 设计目的【ST】
  * 硬件结构区别【A】
  * 特点【R】

* 为什么使用多线程CPU/GPU

  * 任务本身特点出发，计算密集型，可分成子任务，无依赖【Situation】
  * 速度是瓶颈【Task】
  * 多线程CPU/GPU带来的好处【Action】
  * 效果【Result】

* 了解什么c++11特性

  * > 答：auto、大括号初始化。【应该提及：智能指针、nullptr、range for】

* auto的实现

  * > 编译时，推断表达式的类型，以作为变量类型。
    >
    > 必须被初始化否则无法推断。
    >
    > 对于复杂的类型，或者无法表示的类型？【lamda】，简洁
    >
    > 参考：[如何评价 C++ 11 auto 关键字？](https://www.zhihu.com/question/35517805/answer/63304992)

* 花括号list（列表初始化），为什么要设计这个特性【赋值也可用】

  * 提供初始元素值的列表时，只能用花括号初始化。方便对容器进行初始化。与圆括号的直接初始化区分开来。
  * 用于内置类型变量时，如果使用列表初始化且初始值存在信息丢失，则编译器会报错

* 智能指针

* 标准库和STL区别

  * > 答：不知道  
    > C++标准库，C++ Standard Library，是类库和函数的集合，由c++标准委员会制定，并不断维护更新。标准程序库提供若干泛型容器、函数对象、泛型字符串和流（包含交互和文件I/O），支持部分语言特性和常用的函数，如开平方根。C++标准程序库也吸收了ISO C90 C标准程序库。标准程序库的特性宣告于std名字空间之中。  
    > std命名空间是C++中标准库类型对象的命名空间  
    > STL（标准模板库，英文：Standard Template Library，缩写：STL），是一个C++软件库，大量影响了C++标准程序库但**并非是其的一部分**。其中包含4个组件，分别为算法、容器、函数、迭代器。实现了基于模版的数据结构和算法。  
    > **联系**：标准库大量参考了并基于标准模板程序库（STL）所创建的习惯用法，包含容器、算法、迭代器、函数对象等。  
    > 参考：[WIKI：C++标准库](https://zh.wikipedia.org/wiki/C%2B%2B標準函式庫)，[WIKI：STL](https://zh.wikipedia.org/wiki/标准模板库)，[C++标准库/STD/STL等的区别](https://blog.csdn.net/KingCat666/article/details/44995653)

* 项目为什么使用GPU？

* CUDA与C区别
  * > CUDA是一种平台，同样也包括编程语言（CUDA C，CUDA C++，CUDA FORTRAN）  
    > CUDA C/C++，拥有自己的语法，对于CUDA C/C++它支持C++的子集  
    > 参考：[stackoverflow:Cuda C and C++](https://stackoverflow.com/questions/9846523/explanation-of-cuda-c-and-c)

* vector的内存管理。内存占用，如果一开始10000个元素缩小到100，内存会变化吗？一个100元素vector和10000个元素的占用。如何调整vector的空间。

* 静态链接 动态链接
  * > 答：不清楚  
    > [参考](https://www.cnblogs.com/tracylee/archive/2012/10/15/2723816.html)，待补充
    >
    > 动态库b依赖静态库a，程序c依赖动态库b，编译结果会怎样

* 程序与进程区别，编译过程
  * > 同上待补充

* 网络，TCP和UDP的区别，拥塞控制算法流程

* inline 介绍，优势。效率高，为什么开销低。减少函数栈的开销，还有呢？不知道。

  * > 内联函数更快，因为您不需要在堆栈上推送和弹出内容，如参数和返回地址。避免参数拷贝
    >
    > 可重用，减少代码重复，且修改方便。本身是一个函数，会检查参数问题，更安全（可与\#define宏相比）
    >
    > 内联函数应该在头文件中定义，这一点不同于其他函数。编译器在调用点内联展开函数的代码时，必须能够找到 inline 函数的定义才能将调用函数替换为函数代码，而对于在头文件中仅有函数声明是不够的。
    >
    > 参考：[stackoverflow:Benefits of inline function in C++ ](https://stackoverflow.com/questions/145838/benefits-of-inline-functions-in-c)，[When use Inline](https://stackoverflow.com/questions/1759300/when-should-i-write-the-keyword-inline-for-a-function-method/1759575#1759575)

* 会不会多线程，什么时候用多线程，单核心单线程的机器跑多线程会怎么样？

  * > 应该展开讲，思维局限在了高性能应用中
    >
    > 考虑非计算密集型任务【阻塞等情况，交互型】，以及计算密集型任务。考虑OS
    >
    > “有些人把多线程的程序设计和多处理器或多核系统联系起来. 但是即使程序运行在单处理器上, 也能得到多线程编程的好处. 处理器的数量和并不影响程序结构, 所以不管处理器的个数多少, 程序都可以通过使用多线程得到简化. 而且, 即使多线程程序在串行化任务上不得不阻塞, 由于某些线程在阻塞的时候还有另一些线程可以运行, 所以多线程程序在单处理器上运行还是可以改善响应时间和吞吐量。
    >
    > [知乎：单核心CPU的多线程](https://www.zhihu.com/question/37396742)

* 介绍openmp。

  * > 【是什么】【有什么用】【好处】
    >
    > OpenMP是一个可移植跨平台的多线程实现，主线程\(顺序的执行指令\)生成一系列的子线程，并将任务划分给这些子线程进行执行。这些子线程并行的运行，由运行时环境将线程分配给不同的处理器
    >
    > 抽象程度高、易于编程，帮程序员接管负载均衡的问题。可以实现任务并行和数据并行。
    >
    > 用法，\#pragma。
    >
    > 参考：[OMP入门](https://www.cnblogs.com/kuliuheng/p/4059133.html)，[Wiki:OMP](https://zh.wikipedia.org/wiki/OpenMP)

* 两个线程都调用cout，线程1输出A，线程2输出B，输出结果？手写多线程，两个线程轮流cout，保证结果轮流。

  * >   我认为这个问题可以这样考虑，第一个是标准的`cout`是基于流的共享资源，对于多个线程并发执行的情况，实际上由于线程的执行顺序问题，直接输出的话无法控制他们的输出顺序，但是可以保证他们要输出的字符都会被输出。
    >
    >   [参考](https://segmentfault.com/a/1190000016201729#articleHeader2)

---

* 写一个空类

  * > class A{};
    >
    > 大小？
    >
    > C++标准指出，不允许一个对象（当然包括类对象）的大小为0，不同的对象不能具有相同的地址。这是由于：  
    > new需要分配不同的内存地址，不能分配内存大小为0的空间  
    > 避免除以 sizeof\(T\)时得到除以0错误
    >
    > 参考：[C++空类大小](https://blog.csdn.net/lihao21/article/details/47973609)

* new malloc区别

* 智能指针，unique\_ptr能赋值给别人吗·

* 循环引用\(weak\_ptr解决shared\_ptr的循环引用问题\)

* Class struct区别

* C程序内存模型

* 线程进程区别

* 手撸atoi




# 网易笔试3.16

## 约瑟夫环

我用了数组+循环来模拟，不详述。

## 多线程调度问题，求最小总完成时长

参考：https://www.cnblogs.com/cxmhy/p/4477670.html

里面的第二个问题。我用的思路是一样的，奇怪的是只通过了80%？

## 求解线性方程，最小二乘法或梯度下降

写完了没时间调试，所以没通过。

后面重新调了下，渣代码如下：

```c++
#include <iostream>
#include <vector>
#include<string>
using namespace std;

double cal_error(vector<vector<double>> &X, vector<double> &Y,
	vector<double> &error, vector<double> &COE) {
	double sum_err = 0.0;
	for (int i = 0; i<X.size(); ++i) {
		vector<double> &xi = X[i];
		double yi = 0;
		for (int j = 0; j<xi.size(); ++j)
			yi += xi[j] * COE[j];
		error[i] = yi - Y[i];
		sum_err += error[i] * error[i] ;
	}
	return sum_err;
}

void update(vector<vector<double>> &X, vector<double> &Y,
	vector<double> &error, vector<double> &COE, double step, double sum_error) {
	auto grad = X;
	auto rows = X.size(), cols = X[0].size();

	for (int i = 0; i<rows; ++i) {
		auto &xi = grad[i];
		auto erri = error[i];
		for (auto &xxj : xi)
			xxj = xxj * -1.0 * erri * step;
	}
	for (int j = 0; j<cols; ++j) {
		for (int i = 0; i<rows; ++i)
			COE[j] += grad[i][j] * error[j] * error[j] / sum_error;
	}
}

int main() {
	vector<vector<double>> M;
	string tmp;
	while (cin >> tmp) {
		vector<double> v;
		auto last_beg = tmp.begin(), cur = tmp.begin();
		while (cur != tmp.end()) {
			if (*cur == ',') {
				*cur = '\0';
				double tmp = atof(string(last_beg, cur).c_str());
				v.push_back(tmp);
				last_beg = cur + 1;
			}
			++cur;
		}
		double ftmp = atof(string(last_beg, cur).c_str());
		v.push_back(ftmp);
		M.push_back(v);
	}

	vector<double> Y, error, COE(M[0].size(), 1.0);
	vector<vector<double>> X;
	for (auto &vi : M) {
		vector<double> xi(vi.size(), 1.0);
		for (int i = 0; i<vi.size() - 1; ++i)
			xi[i + 1] = vi[i];
		Y.push_back(vi[vi.size() - 1]);
		X.push_back(xi);
	}

	int iter = 0;
	error = Y;
	auto sum_error = cal_error(X, Y, error, COE);
	while (sum_error >= 0.0001) {
		update(X, Y, error, COE, 0.15, sum_error);
		sum_error = cal_error(X, Y, error, COE);
		++iter;
	}

	return 0;
}
```

