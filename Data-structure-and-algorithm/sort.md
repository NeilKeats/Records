> # 常见排序方法
>
> 冒泡、选择排序、插入排序、归并排序、快速排序

# 开始之前

先编写交换元素的函数，以便后续排序时调用。都声明为inline，减少调用开销。

```cpp
//数组形式的
inline void exchange_ele(vector<int> &v, int i, int j){
    int tmp = v[i];
    v[i] = v[j];
    v[j] = tmp;
    return;
};

//迭代器形式的
inline void exchange_ele(vector<int>::iterator i1, vector<int>::iterator i2){
    int tmp = *i1;
    *i1 = *i2;
    *i2 = tmp;
    return;
};
```

# 快速排序

```cpp
void quick_sort_recursive(vector<int>::iterator beg, vector<int>::iterator end){
    if(beg+1 >= end)
        return;
    //这里选第一个元素的值作为中间值
    //实际上也可以选择其他位置的元素，为了方便修改保留了这个变量。
    auto key = beg;
    auto middle = *key;
    auto l_end = beg, curr = beg;
    //swap key and the last
    exchange_ele(key, end-1);
    while(curr != end-1){
        if(*curr<middle){
            exchange_ele(curr, l_end);
            ++l_end;
        }
        ++curr;
    }
    exchange_ele(end-1, l_end);
    quick_sort_recursive(beg,l_end);
    quick_sort_recursive(l_end+1,end);
}

vector<int> &quick_sort(vector<int> &v){
    quick_sort_recursive(v.begin(), v.end());
    return v;
}
```



