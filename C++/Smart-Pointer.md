# 智能指针 与 动态内存





# 智能指针

C++11中有两种智能指针，用于管理动态对象，可以自动释放所指向的对象

1.  `shared_ptr`允许多个指针指向同一个对象
    -   `weak_ptr`是伴随类，弱引用，指向shared_ptr指向的对象
2.  `unique_ptr`则“独占”所指向对象

## `shared_ptr`类

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

**删除器**：默认情况下`shared_ptr<T>`指向动态内存，可以指定函数（删除器,deleter）代替``delete``。删除器函数必须能完成对  ``T*``进行释放的操作。

******`shared_ptr`默认删除器为`delete`，对于动态数组（new []）无法正常调用`delete[]`，需要手动指定删除器。【可能由于】

******此外，`shared_ptr`不支持对动态数组的下标访问。

**循环引用问题：**https://blog.csdn.net/Jacketinsysu/article/details/53341370



## `unique_ptr`类

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



## `weak_ptr`类

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



# 动态内存

## `new`

**`new`**的主要功能：

1.  申请内存(operator new, 或者 operator new[])
2.  调用对象构造函数
3.  返回指向对象的指针

默认情况下，`new`动态分配的对象是默认初始化的（内置类型或组合类型的值未定义）类类型则用默认构造函数初始化。

可以用**直接初始化**方式、**传统构造方式**（圆括号）、**列表初始化**（花括号）、也可以进行值**初始化**

```c++
int *pi = new int(1024);						//直接初始化
string *ps = new string(10, '9');				//传统构造
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

**异常情况**

若`new`不能分配所要求的的空间，就会抛出一个`bad_alloc`异常。

可以指定不抛出错误：（向new传递额外参数的定位new，placement new）

```c++
int *p2 = new (nothrow) int;
```



## **`delete`**

1.  调用析构函数，销毁对象
2.  回收内存(operator delete, 或operator delete[])

`delete`可以对空指针安全操作。需要注意的是，`delete`之后应当重置指针。

******可以合法`delete`空指针

******编译器无法分辨指针指向静态、动态对象，以及动态指针指向的内存是否已经释放。因此，释放静态对象地址、释放两次同一个指针，是未定义的



## 动态数组

### 使用`new T[]`

`new T[]`可以分配“动态数组”，但实际上并不得到**数组类型**，而是一个数组元素类型的指针。因此，无法使用`begin`和`end`函数，此外用`sizeof`也无法获得维度信息，也不能用范围for（数组类型包含维度信息）。

**初始化动态分配对象的数组**

```c++
int *pia1 = new int[10]; 			//默认初始化
int *pia2 = new int[10](); 			//值初始化
int *pia3 = new int[10]{0,1,2,3};	//前三个使用初始化器，后面元素使用值初始化
```

**异常情况**

失败则抛出`bad_array_new_length`异常，与`new`相似

**分配空数组合法**

返回指针不与其他指针相同的，就如尾后指针。

### `delete[]`

```c++
delete p;		//p必须指向一个动态分配的对象或为空
delete[] pa;	//pa必须指向一个动态分配的数组或为空
```

`delete []`完成的工作：

1.  销毁数组中的元素（逆序销毁，最后一个先被销毁，第一个最后销毁）
2.  释放内存

******释放指向数组的指针时，必须使用**`[]`**：指示编译器指针指向对象数组的第一个元素。若只用了`delete`则行为是未定义的。

### 智能指针与动态数组

标准库提供了一个可以管理`new`分配的数组的`unique_ptr`版本。用法：

```c++
unique_ptr<T[]> up(new T[10]);
up.release(); //自动调用delete[]销毁指针
```

与普通的`unique_ptr`略有不同，支持下标访问，不支持点和箭头的成员运算符。

**`shared_ptr`**不直接支持管理动态数组，需要自己定义删除器。也不支持下表访问。



##allocator类

`allocator`是一个模版，将内存分配与对象构造分离。可以分配大块未构造内存（unconstructed），真正需要时才执行对象构造。	

```C++
allocator<string> alloc;			//
auto const p = alloc.allocate(n);	//分配n个未初始化的string
```

### **构造对象**

```c++
a.construct(p, args);		//p必须为一个类型为T*的指针，指向原始内存；args为传递给T类型构造函数的参数，用于构造对象

auto q = p ;    //q指向最后构造的元素之后的位置（用于构造）
alloc.construct(q++);
alloc.construct(q++, 10, 'c');
alloc.construct(q++, "hi");
```

使用未构造内存，行为未定义

### **销毁对象与释放内存**

```c++
while(q!=p)
  alloc.destory(--q);

alloc.deallocate(p,n);	//p不能为空
```

### **拷贝/填充未初始化内存**

```c++
uninitialized_copy(b,e,b2);		//从b开始到e，范围内的元素拷贝到b2开始的内存中
uninitialized_copy_n(b,n,b2);	//从b开始的n个元素，拷贝到b2开始的内存

uninitialized_fill(b,e,t);		//从b到e到原始内存中，创建对象，值为t的拷贝
uninitialized_fill_t(b,n,t);

```

返回递增后的目标位置迭代器。