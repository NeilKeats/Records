# 智能指针

C++11中有两种智能指针，用于管理动态对象，可以自动释放所指向的对象

1.  `shared_ptr`允许多个指针指向同一个对象
    -   `weak_ptr`是伴随类，弱引用，指向shared_ptr指向的对象
2.  `unique_ptr`则“独占”所指向对象

# `shared_ptr`类

允许多个指针指向同一个对象，默认初始化的智能指针包含一个空指针。

`shared_ptr`的独有操作包括

```c++
make_shared<T>(args); 	//返回一个shared_ptr，指向一个动态分配的类型T对象，使用args初始化
shared_ptr<T>p(q); 		//p是shared_ptr q的拷贝：递增q的计数器；q的指针必须能转换为T*
p = q;					//两者都是shared_ptr，所保存指针必须能互相转换。递减p原来指向对象的引用计数，递增q的引用对象。（若p原指向对象计数变为0，则自动销毁）
p.unique();				//若使用p.use_count()为1，则返回true，否则false
p.use_count();
```

安全的分配方法是调用`make_shared<T>`，在动态内存中分配对象并**初始化**，返回指向这个对象的`shared_ptr`。

```c++
shared_ptr<int> p1 = make_shared<int>(42);
shared_ptr<int> p2 = make_shared<int>();
shared_ptr<string> ps = make_shared<string>(10,'9');
```

也可以结合`new`进行`shared_ptr`的初始化，需要注意的是接受**指针参数**的智能指针构造函数是**explicit**的，不能隐式转换，必须直接初始化形式。

```c++
shared_ptr<T> p(q);		//p管理内置指针q指向对象，且q必须指向new分配内存且类型能转换为T*
shared_ptr<T> p(u);		//从unique_ptr u接管对象所有权，将u置为空
shared_ptr<T> p(q,d);	//内置指针q，d为可调用对象用于代替delete
shared_ptr<T> p(p2,d);	//p是shared_ptr p2的拷贝，区别在于p调用d来delete

p.reset();
p.reset(q);
p.reset(q,d);
//例
shared_ptr<int> p1 = new int(1024);	//错误
shared_ptr<int> p2(new int(1024));	//正确
```

初始化的指针必须指向动态内存，因为默认使用delete释放关联的对象（也可以自己提供操作代替delete）。

`shared_ptr`有一个计数器，称之为引用计数reference count。

`shared_ptr`的**析构函数**，递减引用对象的计数，若计数减为0则销毁指向对象并释放内存【delete? 依实现而定可能】：

>   参考：[C++11的shared_ptr源代码剖析](https://blog.csdn.net/PROGRAM_anywhere/article/details/78204676)

不`delete` get()返回的指针，不要将get()用于初始化另一个智能指针，因为这样两个智能指针没有关联，会引发意外的delete(新智能指针销毁时隐式delete)。

**智能指针与异常**：发生异常时，程序块过早结束，也能正确回收智能指针所指向的内存（因为局部对象智能指针会被销毁）。

**删除器**：默认情况下`shared_ptr<T>指向动态内存，可以指定函数（删除器,deleter）代替`delete`。删除器函数必须能完成对  ``T*``进行释放的操作。



**循环引用问题：**https://blog.csdn.net/Jacketinsysu/article/details/53341370



# `unique_ptr`类

“独占”所指向对象，指针被销毁时对象也被销毁。

定义时，没有类似`make_shared<T>`，必须使用直接初始化形式：

```c++
unique_ptr<double> p1;				//可指向一个double的unqiue_ptr
unqiue_ptr<int> p2(new int(42));	//指向值为42的int
```

**不支持拷贝与赋值**，但可以通过`reset()`和`release()`将指针所有权从一个（非const）的unqiue_ptr转移到另一个

```c++
unqiue_ptr<string> p1("new string");
unqiue_ptr<string> p2(p1);			//错误，不支持拷贝
unqiue_ptr<string> p3;
p3 = p1;							//错误，不支持赋值
//
unqiue_ptr<string> p2(p1.release());	//release将p1置空
unqiue_ptr<string> p3(new string("Trex"));
p2.reset(p3.release());					//reset释放p2原来指向的内存
```

`release()`返回保存的指针并将`unqiue_ptr`置空。

`reset()`接受一个可选参数，将`unqiue_ptr`指向给定的指针，如果原先`unqiue_ptr`不为空，则先释放原对象。可以直接调用`u.reset()`则直接释放u的对象并置空u。

**特殊：可以拷贝或赋值一个将要被销毁的unqiue_ptr**

实际上是使用了移动语义（移动拷贝）。

例：一个函数返回一个unqiue_ptr

```c++
unqiue_ptr<int> clone(int p){
  return unqiue_ptr<int> (new int(p)); //正确
}

unqiue_ptr<int> clone_2(int p){
  unqiue_ptr<int> ret(new int(p));
  return ret;	//正确
}
```

**删除器**

与`shared_ptr`不同，`unqiue_ptr`的删除器是**类型的一部分**，重载删除器会影响到`unqiue_ptr`类型以及如何构造该对象的类型。必须在尖括号中指定删除器的类型。

```c++
unqiue_ptr<objT, delT> p (new objT, fcn);
```

原因在于，删除器是`unqiue_ptr`的一部分，删除器成员的类型在**编译时绑定**，保存在对象中。实际上删除时执行如下代码

```c++
//unqiue_ptr的del在编译时绑定；直接调用实例化的删除器
del(p);
```

与此相比，**`shared_ptr`**的删除器并不是类型一部分，是**运行时绑定**的，可以随时改变删除器的类型（使用`reset`）。但是类成员的类型在运行时无法改变，不能直接保存删除器在类中。删除器是间接保存的，通过一个成员`del`来访问。【根本原因是 shared_ptr并不独占指针，考虑`shared_ptr`动态多态的情况，很可能需要指定删除器】。删除时代码如下：

```c++
//del的值在运行时才知道
del? del(p) : delete p; 	//del(p)运行需要转跳到del的地址
```

# weak_ptr

不控制所指向对象生存周期的智能指针，指向一个由`shared_ptr`管理的对象。绑定到一个`shared_ptr`不会增加其计数，一旦最后一个`shared_ptr`被销毁对象也会被销毁。

```c++
weak_ptr<T> w;		//空的weak_ptr<T>可以指向类型为T的对象			
weak_ptr<T> w(sp);	//与shared_ptr指向相同对象的weak_ptr，T必须能转换为sp指向的对象
w = p;				//p可以为weak_ptr或者shared_ptr，赋值后共享对象
w.reset();			//置空w
w.use_count();		//与w共享对象的share_ptr的数量
w.expired();		//若w.use_count()为0，返回true，否则false
w.lock();		//如果expired()为true，则返回空的shared_ptr，否则返回一个指向w对象的shared_ptr
```

创建时要用shared_ptr来初始化：

```c++
auto p = make_shared<int>(42);
weak_ptr<int> wp(p);
```

**由于对象可能不存在**，不能使用`weak_ptr`直接访问对象，必须调用`lock()`，此函数检查weak_ptr指向的对象是否存在。存在则返回一个指向共享对象的`shared_ptr`。

```c++
if(shared_ptr<int> np = wp.lock()){//若np不为空则成立
	//在if中，np与wp共享对象
}
```



