# 

# 语句 statement

## 空语句

```
;//空语句
```

## **复合语句**：

花括号括起来的语句和声明序列，也成为块

## 范围for：

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

## try语句：

```cpp
    try{
        program-statements
    } catch (exception-declaration){
        handler-statements
    } catch (exception-declaration){
        handler-statements
    } //..
```



