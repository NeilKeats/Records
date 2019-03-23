# 文件I/O

>   `open`、`read`、`write`、`lseek`、`close`
>
>   这部分函数被称为unbuffered I/O，是调用内核的一个系统调用，不是ISO C的组成部分，而是POSIX的部分。

# 文件描述符

是一个非负整数，当打开一个现有或创建一个新的文件时，内核向进程返回一个文件描述符。作用域是进程。

>   通常0为标准输入，1为标准输出，2为标准错误输出
>
>   幻数0、1、2应替换为符号常量`STDIN_FILENO` 、`STDOUT_FILENO`、`STDERR_FILENO`

文件描述符范围为0~`OPEN_MAX`（每个进程最大文件打开数）



# `open`函数

打开或创建一个文件

```c++
#include<fcntl.h>

int open(const char *pathname, int oflags, .../* mode_t mode*/);
```

成功返回文件描述符，出错返回-1。返回的文件描述符一定是最小的未使用描述符数值。

`oflags`用多个常量进行**或**运算构成，但读写三个只能选一。

```c++
O_RDONLY	//只读打开
O_WRONLY	//只写打开
O_RDWR		//读写打开
//
O_APPEND	//每次写都追加到末尾
O_CREATE	//若文件不存在，则创建它
O_EXCL		//用于“测试并创建文件”的原子操作模式
O_TRUNC		//若文件存在，且只写或读写模式，则将长度截断为0
O_NOCTTY	//
O_NONBLOCK	//若是FIFO、块特殊文件、字符特殊文件，则表示使用非阻塞模式
//
O_DSYNC		//每次write都等待物理I/O操作完成，如果写操作不影响读取刚输入的数据，则不等待文件属性被更新
O_RSYNC		//
O_SYNC		//每次write都等待物理I/O操作完成，包括由write操作引起的文件属性更新所需的I/O
```

`O_DSYNC`仅当文件属性需要更新以反映文件数据变化时，O_DSYNC标志才影响文件属性。

`O_SYNC`设置后，数据总是和属性 同步更新。

# `create`函数

```C++
#include <fcntl.h>

int create(const char *pathname, mode_t mode);
//等价于
open(pathname, O_WRONLY | O_CREAT | O_TRUNC, mode);
```

返回值与`open`类似。

`close`缺点在于以**只写**的方式打开创建的文件。需要读的情况可以用open。

# `close`函数

```c++
#include <unistd.h>

int close(int filedes);
```

成功则返回0，出错返回-1；

**关闭文件时，释放该进程加在文件上的所有记录锁**

进程结束时，内核自动关闭它打开的所有文件。因此，可以不显式调用`close`

# `lseek`函数

用于显示地设置 **当前文件偏移量**【描述文件开始处开始计算的字节数偏移】

默认情况下，打开一个文件，偏移量为0【指定O_APPEND除外】

```c++
#include <unistd.h>

off_t lseek(int filedes, off_t offset, int whence);
//off_t是带符号数
```

 成功则返回新的偏移量，出错则返回-1

对`offset`的解释与参数`whence`的值有关

-   `SEEK_SET`，则偏移量设置为距离开始处`offset`个字节
-   `SEEK_CUR`，则设置为**当前值**加`offset`（`offset`可正可负）
-   `SEEK_END`，设置为**文件长度**加`offset`（`offset`可正可负）

>   对FIFO、管道、socket，都返回-1，且`errno`设置为ESPIPE
>
>   比较返回值时，不应该比较小于0，而应该与-1比较

`lseek`将偏移量记录在内核，并不引起I/O操作。可以大于文件长度，这种情况下的下一次写入将加长文件长度，并形成**空洞**，没写过的字节为0，且不占用磁盘存储空间。

# `read`函数

>   在用户空间没buffer，但在内核有

```c++
#include <unistd.h>

ssize_t read(int filedes, void *buf, size_t nbytes);
```

read成功返回读到的字节数，若达到文件末尾则返回0

`*buf`表示通用指针

有可能读到的字节数少于所要求的字节数：

-   读普通文件时，在读到要求的字节数之前到达文件尾端。若离文件尾端只有30个字节，请求读100个字节，则`read`返回30。下一次再调用`read`时返回0
-   终端设备最多读取一行
-   网络读，缓冲机构可能造成返回值小于所要求读字节数
-   管道/FIFO，包含字节数少于所需的数量
-   信号造成中断，而已经读了一部分数据

# `write`函数

```c++
#include <unistd.h>

ssize_t write(int filedes, const void *buf, size_t nbytes);

```

返回值通常与`nbytes`相同，否则表示出错。出错可能：磁盘已满、超过一个给定进程的文件长度限制

# 文件共享

内核使用三种数据结构表示打开的文件

1.  **每个进程**在进程表有一个记录项，记录项中包含有一张**打开文件描述符表**。其中每个**文件描述符**占一项。文件描述符包括：
    -   文件描述符标志
    -   指向**文件表项**的指针
2.  **内核**为所有打开文件维持一张文件表，每个**文件表项**包含：
    -   文件状态标志（读/写、同步、非阻塞等）
    -   当前文件偏移量
    -   指向该文件的v节点表项的指针
3.  每个打开文件都有一个v节点，包含了文件类型和对文件进行各种操作的函数的指针。对于大多数文件，v节点还包括了该文件的i节点。【Linux没有使用v节点而是使用了通用i节点结构】

**注意：**

**\*\*** **文件描述符**是进程内有效的

**\*\***多个**文件描述符**，可以指向同一个**文件表项**【参考父子进程fork】

**\*\***多个**文件表项**，可以指向同一个文件

>   可参考：
>
>   https://blog.csdn.net/test1280/article/details/80293294



如上图，两个进程独立打开了同一个文件，各自得到一个文件表项，指向同一个v节点。【如此设计的理由：不同进程对该文件有自己的偏移量】

-   在完成`write`后，文件表项中的当前文件偏移量增加所写字节数。（超过文件长度则加长文件）
-   使用O_APPEND标志打开文件，则相应标志也被加到文件状态标志中。对具备这种标志的文件写时，偏移量首先被设置为i节点表项中的文件长度。使得每次都添加到文件的当前尾端
-   若用`lseek`定位到文件当前的尾端，则文件表项的偏移量也被设置为当前文件长度【注意，与设置O_APPEND不同】

# 原子操作

考虑进程，添加数据到文件末尾

```c++
if (lseek(fd, 0L, 2) < 0)
	err_sys("lseek error");			/*position to EOF*/
if (write(fd, buf, 100) != 100)
	err_sys("write error");			/*and write*/
```

对于两个独立的进程，若对同一个文件进行添写，当不使用`O_APPEND`标志，数据结构如上一节图所示。

目标是两个进程都append文件，但可能出现重复覆盖的情况，因为上面两个系统调用之间可能被内核挂起进程。

对于其他进程而言，上述的两个操作都是**原子操作**，但两个操作不可能是原子的。

UNIX提供了一种方法，使这种操作变成原子操作。

## `pread`和`pwrite`函数

```c++
#include <unistd.h>

ssize_t pread(int filedes, void *buf, size_t nbytes, off_t offset);

ssize_t pwrite(int filedes, void *buf, size_t nbytes, off_t offset);
```

相当于顺序调用`lseek`和`read`/`write`，但不会被中断。

-   调用`pread`时，无法中断其定位和读操作
-   不更新文件指针

## 创建文件

若先调用`open`检查文件是否存在，再调用`create`创建文件。则同样可能出现问题，因为两次调用不是原子的。

# `dup`与`dup2`

```c++
#include <unistd.h>

int dup(int filedes);
int dup2(int filedes, int filedes2);
//返回文件描述符若成功，返回-1若失败
```

`dup`返回最小的可用文件描述符；

`dup2`的参数指定新的描述符数值，若`filedes2`已经存在，则先关闭它；若相等，则返回`filedes2`不关闭。且`dup2`是原子操作（关闭以及“复制”）。

# `sync`、`fsync`和`fdatasync`函数

内核中设有缓冲区高速缓存，或页面高速缓存。

延迟写（delayed write）机制：写入文件时，先将数据复制到其中一个缓冲区中，如果缓冲区未满，则不将其排入输出队列，而等待**写满**或内核需要**重用该缓冲区**以便存放其他磁盘数据块，再将缓冲排入输出队列，到达**队首**时，进行实际I/O操作。

```c++
#include <unistd.h>

int fsync(int filedes);
int fdatasync(int filedes);
void sync(void);
```

`sync`将所有修改过的块缓冲区排入写队列，返回，**不等待**写操作结束。【系统守护进程update会周期性地调用sync】

`fsync`对`filedes`指定的单一文件起作用，且**等待**写操作再返回。【同步更新文件属性】

`fdatasync`类似`fsync`，它只影响文件的数据部分。

# `/dev/fd`

系统提供名为`dev/fd`的目录，其目录项为0、1、2等文件，打开文件`/dev/fd/n`等效于复制描述符n

如`/dev/fd/stdin`等

