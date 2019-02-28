> # 常见排序方法
>
> 冒泡、选择排序、插入排序、归并排序、快速排序

# 开始之前

先编写交换元素的函数，以便后续排序时调用。都声明为inline，减少调用开销。

```cpp
//下标形式的
inline void swap(vector<int> &v, int i, int j){
    int tmp = v[i];
    v[i] = v[j];
    v[j] = tmp;
    return;
};

//迭代器形式的
inline void swap(vector<int>::iterator i1, vector<int>::iterator i2){
    int tmp = *i1;
    *i1 = *i2;
    *i2 = tmp;
    return;
};
```

# 快速排序

```cpp
/*
 * quick sort
 */
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

void quick_sort(vector<int> &v){
    quick_sort_recursive(v.begin(), v.end());
    return;
}
```

# 堆排序

```cpp
/*
 * Heap sort
 */
void siftdown(vector<int> &v, int i, int len){
    //for i (start from 0 to n-1 )
    //left   : 2*i + 1
    //right  : 2*i + 2
    //father : (i-1)/2
    int son = 2*i + 1;
    if(son>=len)
        return;
    if((son+1) < len && v[son+1] > v[son] )
        ++son;
    if(v[i]>= v[son])
        return;
    swap(v, i, son);
    //recursive
    return siftdown(v, son, len);
}

void heap_sort(vector<int> &v){
    //sift down from the last father to the root
    //O(N)
    for(int i=(v.size()-2)/2; i>=0; --i)
        siftdown(v,i,v.size());

    //O(NlogN)
    for(int i=v.size()-1; i>0; --i){
        swap(v,0,i);
        //the last element swap to the first 
        //then go down.
        siftdown(v,0,i);
    }

    return;
}
```

参考：

[https://stackoverflow.com/questions/9755721/how-can-building-a-heap-be-on-time-complexity](https://stackoverflow.com/questions/9755721/how-can-building-a-heap-be-on-time-complexity "如何在O\(N\)时间内建堆")

[https://zh.wikipedia.org/wiki/%E5%A0%86%E6%8E%92%E5%BA%8F](https://zh.wikipedia.org/wiki/%E5%A0%86%E6%8E%92%E5%BA%8F "Wiki.堆排序")



