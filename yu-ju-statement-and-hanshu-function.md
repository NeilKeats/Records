# 1. 语句 statement

## 1.1 空语句

```
;//空语句
```

## 1.2 复合语句

花括号括起来的语句和声明序列，也成为块

## 1.3 范围for

不能通过范围for向vector等对象增加元素，因为预存了end的值，可能失效。

```cpp
    for (auto &r : v)
        r *= 2;
    //等价于
    for(auto beg = v.begin(), end=v.end(); beg!= end; ++beg){
        auto &r = *begin;
        r *= 2;
    }
```

## 1.4 try语句

```cpp
    try{
        program-statements
    } catch (exception-declaration){
        handler-statements
    } catch (exception-declaration){
        handler-statements
    } //..
```

---

# 2. 函数

> **形参**（parameter）
>
> **实参**（argument）：形参的初始值，实参求值顺序不规定。
>
> **调用运算符**（call operator）: `()`
>
> **函数调用**完成两项工作
>
> 1. 用实参初始化形参（隐式定义并初始化
> 2. 将控制权转交给被调用函数（主函数此时被暂时中断）。
>
> **return语句**结束函数执行过程，完成两个工作：
>
> 1. 返回return语句中的值（若有）
> 2. 将控制权从被调函数转回主调函数。不能返回数组、函数，但可以返回指向数组或者函数的指针

## 2.1 局部对象与生命周期

**名字**有**作用域**（在其中可见）

**生命周期**是该对象存在的一段时间

**局部对象**：形参、函数体内部定义的变量，为局部对象（local variable）

* **自动对象**：函数控制路径经过变量定义语句时创建，块尾部销毁。只存在于块控制期间的对象为自动对象。形参为实参初始化的自动对象，对于自动对象若无初始值则默认初始化。
* **局部静态对象**（Local static object）：生命周期贯穿函数调用及之后的时间。在执行路径第一次经过对象定义语句时初始化，程序结束时销毁。

## 2.2 分离式编译

* 修改了其中一个源文件，只需要重新编译那个改动了的文件。产生后缀为.obj\(Win\)或.o\(UNIX\)的文件，含义为该文件包含对象代码\(object code\)。
* 编译器将对象文件链接成可执行文件

---

# 3. 函数参数传递

> 实参对形参初始化。
>
> 形参为引用类型，则绑定到对应实参（**引用传递**）；
>
> 否则值拷贝后赋值给形参（**值传递**）。

## 3.1 const形参和实参

实参**初始化**形参时**忽略顶层const**。形参为const时，传给它常量或非常量都可以。

```cpp
void fcn(const int i) {}
void fcn(int i){} //错误，重复定义func(int)
```

虽然函数可以重载，但是**形参列表**应该有区别。上述两个函数参数可以完全一样，因此第二个定义有错。（guess：顶层const无法重载.？）

## 3.2 指针或引用形参与const

* 可以用非常量初始化底层const，反之不行。
* 只有**引用传递**和**指针传递**可以用是否加**const**来重载。

## 3.3 尽量使用常量引用

* 传入参数限制：无法传入const对象。
* 适应性：其他函数正确定义const引用，但非常量引用的函数无法在其中调用

## 3.4 数组形参

**数组**特点：不允许拷贝数组、使用时通常转换为指针

无法值传递，传递时数组会被转换为指针，数组大小无影响。

形参可写为：

```cpp
    void print(const int*);
    void print(const int[]);
    void print(const int[10]); //表示期望数组的元素数目，但实不一定如此
```

上述三个函数等价，形参都为 `const int*`，传入时只检查参数是否为`const int *`。

### 3.4.1 **指定数组大小**

1. 用**标记**指定数组结束，如字符数组的`'\0'`，读到表示数组结束

2. 使用**标准库规范**：传入数组首元素、尾后元素的指针

3. **显式传递**数组大小：

### 3.4.2 数组引用形参和const

变量（形参）可定义为数组的引用

```cpp
//正确：形参是数组的引用，维度是类型的一部分
void print(int (&arr)[10]){
for (auto elem : arr)
cout<< elem << endl;
}

//&arr两端括号不可省略
f(int &arr[10]); //错误，arr声明为引用的数组
f(int (&arr)[10]);
```

数组大小是构成数组类型的一部分，不超过维度都可在函数体内使用。

当不需要对数组元素执行**写操作**时，**形参**应该为指向`const`的指针。

### 3.4.3 多维数组

C++没有真正意义的多维数组，是数组的数组。

传递多维数组时，传递的是指向数组首元素的指针。

```cpp
    //matrix指向数组的首元素，该数组的元素是由10个整数构成的数组。matrix括号不可忽略！
    void print(int (*matrix)[10], int rowSize){};
```

也可以使用数组语法定义，第一个维度会被忽略因此不适宜放在形参列表中

```cpp
    //等价定义
    void print(int matrix[][10], int rowSize){};
```

## 3.5 可变形参的函数

1. 使用initializer\_list标准库类型
2. 特殊形参类型：省略符。一般只用于与C函数交互的接口

---

# 返回类型与return语句

返回值的方式和初始化形参、变量的方式一样：返回值用于初始化调用点的一个临时量，临时量就是函数调用的结果。

* **返回引用**，引用为所引对象的别名。因此，**不返回局部对象的引用或指针**。若返回引用得到左值，其他返回类型得到右值。

```cpp
    const string &manip(){
        //局部变量
        string ret;
        //某些处理ret
        if(!ret.empty())
            return ret; //错误：返回局部对象引用
        else
            return "Empty";    //错误："Empty"是一个局部临时量
    ｝
```

* **列表初始化返回**，用花括号初始化。

```cpp
    vector<string> process(){
        // ...
        return {"functionX","Okay};
    }
```

* **返回数组指针**

  * **数组特点**：不可拷贝，因此可以返回数组指针或引用。
  * **声明一个返回数组指针的函数：**
  * ```cpp
    int arr[10];
    int *p1[10]; //含有10个指针的数组
    int (*p2)[10] = &arr; //指向含有10个整型的数组的指针
    ```
  * 若要返回一个数组指针，**数组维度必须跟在函数名字之后**。形参也跟在函数名前且先于数组维度。

  * ```cpp
    Type (*function(parameter list)) [dimension];
    int (*func(int i))[10];

    //使用类型别名代替
    typedef int arrT[10];
    using arrT = int[10];
    arrT* func(int i);
    ```
  * 使用**尾置返回类型**（C++11）【也可使用decltype】
  * ```cpp
    auto func(int i) -> int(*)[10]
    ```

**主函数main**

* 可以没有`return`语句，编译器隐式插入返回`0`的`return`语句。
* 不可调用自己

**递归**：直接、间接调用自己

---

# 函数重载

同一个 作用域内，几个函数名字相同但形参不同，称之为重载函数

> \#main函数不能重载
>
> \#不允许两个函数除了返回类型外，其他要素相同

## 重载与const形参

如上所述，顶层const不影响传入函数的对象。一个有顶层const的形参无法与另一个无顶层const的形参区分。

```cpp
    int func(int);
    int func(const int); //重复声明

    int func_(int*);
    int func_(int* const); //顶层const,重复声明
```

底层const可以实现重载

```cpp
    int func(int &);
    int func(const int &); //合法重载，常量引用

    int func_(int*);
    int func_(const int*);
```

底层const只能传给const。由于非常量可以转换为const，非常量引用、指向非常量的指针可以作用于以上四个函数，在调用时，编译器将非常量的参数优先调用非常量的重载函数。

## const\_cast与重载

```cpp
    //常量引用版本
    const string &shorter_string(const string &s1, const string &s2){
        return s1.size()<=s2.size()?s1:s2;
    }
    //非常量引用版，需要一个普通引用返回
    //可以使用const_cast
    string &shorter_string(string &s1, string &s2){
        auto &r = shorter_string(const_cast<const string&>(s1),
                                 const_cast<const string&>(s2));
        return const_cast<string&>(r);        
    }
```

## 调用重载的函数

**函数匹配（function matching）**，又叫**重载确定（overload resolution）    **

把函数调用与一组重载函数中的某一个关联起来。将调用的实参与重载集合中的每一个函数的形参进行比较，再根据比较结果决定调用哪个：

* 找到一个与实参最佳匹配的（best mtach）的函数，生成调用的代码。
* 找不到任何一个匹配，编译器发出无匹配（no match）的错误。
* 有多于一个的函数可以匹配，但每一个都不是明显更好的选择。发生错误，二义性调用（ambiguous call）

## 重载与作用域

如果在内层作用域声明名字，将隐藏外层作用域中的**同名实体**。不同作用域中无法重载。

> \#C++中，名字查找发生在类型检查之前。



