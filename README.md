# 概念：

**表达式 expression**， 对表达式求值得到一个结果，字面值和变量是最简单的表达式。把一个运算符与一个或多个运算对象组合起来可以得到复杂表达式

**运算对象 operand                                                                              
**

**运算符 opeartor**：一元unary、二元binary

组合运算符与运算对象，多个运算符的表达式，需要考虑**优先级**、**结合律**、**求值顺序**

**运算对象转换**，将运算对象从一个类型转为另一个类型

**重载运算符**，无法改变优先级、结合律、运算对象个数

**左值与右值**

* 左值表达式：求值结果是一个对象或者函数。
* 对象用于右值时，用的是对象的值（内容），用做左值时，用的是对象的身份（在内存中位置）。
* 左值可代替右值使用，反之不行

---

# 优先级，结合律，求值顺序

* **优先级**：哪个运算符先组合（eg. \*优先级比+高）
* **结合律**：左结合律，则相同运算符从左到右开始顺序组合（eg. std::cout&gt;&gt;i1&gt;&gt;i2&gt;&gt;endl; &gt;&gt;是左结合律）
* 括号无视组合规则
* **求值顺序**：大多数情况不明确表达式中运算对象的求值顺序。
  * 4个运算符规定求值顺序：逻辑与（&&）、逻辑或（\|\|）、逗号（,）、条件运算符（?:）

```cpp
    Int i = f1()*f2(); //不确定发f1与f2的调用顺序；

    std::cout<< i<< " "<< ++i <<std::endl; //未定义的。

    While(beg != s.end() && !isspace(*beg))
        *beg = toupper(*beg++)  //错误，该语句未定义
```

**例**

```cpp
f()+g()*h()+j();
```

优先级：g\(\)返回值先与h\(\)返回值相乘

结合律：f\(\)返回值先于g\(\)\*h\(\)返回相加，结果再与j\(\)返回值相加

求值顺序：这些函数调用顺序无明确规定

---

# 运算符

## 算数运算符

除非特殊说明，能用于任意算数类型；运算对象、求值结果都是右值。

取模、取余运算：C++规定商向0取（切除小数部分）。要求： \(m/n\)\*n + m%n == m，即m%n若不为0则与m同号

## 逻辑和关系运算符

与、或：左侧为真时才计算右侧对象（**短路求值 short-circuit evaluation**）

## 关系运算符（&gt;,&gt;=,&lt;,&lt;=,==）

进行比较运算时除非比较对象是bool，否则不使用bool字面值true和false

## 赋值预算符

左侧运算对象必须是可修改的**左值**，结果也是左侧运行对象的左值。**满足右结合律**

C++11允许花括号初始化

```cpp
vector<int> vi={1,2,3,4,5};
```

## 递增、递减运算符

前置版本：以**左值**形式返回对象本身

后置版本：以**右值**形式返回对象原始值副本（非必要时不用，由于副本存在可能影响性能）

## 成员访问运算符

指针类型`ptr`，`ptr->mem`等价于`(*ptr).mem`

箭头运算符`->`，结果是左值

点运算符`.`，若成员是左值则结果为左值，反之为右值

## 条件运算符?:

可嵌套，右结合律，从右到左组合。

优先级低，长表达式中嵌套了条件运算符子表达式，通常需要加括号

## 位运算符

|  |  |
| :---: | :---: |
| ~ | 位求反 |
| &lt;&lt; | 左移 |
| &gt;&gt; | 右移 |
| & | 位与 |
| \| | 位或 |
| ^ | 位异或 |

左移/右移，**右侧运算对象不能为负值**且不可超过结果位数，移出界外的值舍弃

## 移位运算符

```cpp
cout<<"hi"<<" there"<<endl;
```

左结合律，返回左值。运算优先级比关系、赋值、条件运算符的优先级高。

## sizeof运算符

右结合律，返回`size_t`类型的常量表达式

并不实际计算运算对象的值，因此对无效指针`p`运算的`size(*p)`是安全的

## 逗号运算符

含有两个运算对象，按从左到右求值。先求左侧表达式的值，并抛弃求值结果，真正结果是右侧表达式的值（若右侧表达式运算对象是左值，则求值结果也算左值）

---

# 类型转换

## 算术转换

**整型提升**：小整数类型转换为大整数类型，保证提升为能容纳原类型所有可能的值的大类型

无符号类型的运算对象：运算对象的类型不一致，需转为同一种类型。先整型提升，若匹配无需进一步转换。若一个为有符号、另一个为无符号，只有无符号的对象的所有可能值可以被有符号类型容纳，才将无符号对象转换为有符号类型（其他情况将有符号对象转为无符号类型）。

例：

```cpp
short sval;
char cval;
int ival;
long lval;
unsigned short usval;
unsigned int uival;
sval + cval; //sval和cval提升为int
usval + ival; //根据unsigned short 和 int所占空间大小进行提升
uival + lval; //根据unsigned int 和 long所占空间的大小进行转换
```

## 其他类型隐式转换

数组转换为指针。当数组用作`decltype`、`sizeof`、`&`取址符、`typeid`时，不发生以下转换。

```cpp
    int ia[10];
    int *ip = ia;
```

整数`0`、字面常量值`nullptr`能转换为任意指针类型；指向任意非常量的指针能转换为`void*`；指向任意对象的指针能转换成`const void*`。

## 显式转换

**强制类型转换**

```cpp
cast-name<type>(expression);
//cast-name为 static_cast、dynamic_cast、const_cast和 reinterpret_cast
```

`Dynamic_cast`：支持运行时类型识别

`Static_cast`：任何具有明确意义的类型转换，只要不包含底层`const`都可用。可用于较大算数类型转换为较小类型、`void*`转换为其他类型指针

`Const_cast`：只能改变运算对象的底层`const`

```cpp
    const char *pc;
    char *p = const_cast<char*>(pc); //正确，但通过p写值是未定义行为
```



