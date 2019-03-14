> **常见排序方法**
>
> 冒泡排序、选择排序、插入排序、归并排序、快速排序、堆排序

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
```

```cpp
//迭代器形式的
inline void swap(vector<int>::iterator i1, vector<int>::iterator i2){
    int tmp = *i1;
    *i1 = *i2;
    *i2 = tmp;
    return;
};
```

**简单小结不同排序算法**

|          | 与输入有关  | 稳定性  |   平均时间    |    最坏     |     最好     |  空间复杂度   |
| :------- | :----: | :--: | :-------: | :-------: | :--------: | :------: |
| **冒泡排序** | 有（优化版） |  ✔   |  $$N^2$$  |     -     | $$N$$(优化版) | $$O(1)$$ |
| **插入排序** |   有    |  ✔   |  $$N^2$$  |  $$N^2$$  |   $$N$$    | $$O(1)$$ |
| **选择排序** |   无    |  ✖   |  $$N^2$$  |     -     |     -      | $$O(1)$$ |
| **归并排序** |   无    |  ✔   | $$NlogN$$ |     -     |     -      | $$O(N)$$ |
| **快速排序** |   有    |  ✖   | $$NlogN$$ |  $$N^2$$  | $$NlogN$$  | $$O(1)$$ |
| **堆排序**  |   有    |  ✖   | $$NlogN$$ | $$NlogN$$ | $$NlogN$$  | $$O(1)$$ |
|          |        |      |           |           |            |          |
|          |        |      |           |           |            |          |
|          |        |      |           |           |            |          |
|          |        |      |           |           |            |          |



# 冒泡排序

```cpp
/*
 * bubble sort
 */
void vbubble_sort(vector<int> &v){
    int len = v.size();
    for(int i = len-1; i>=0; --i)
        for(int j = 0; j<i; ++j )
            if(v[j]>v[j+1])
                swap(v,j,j+1);
    
    return;
}
```

# 选择排序

```cpp
/*
 * selection sort
 */
void selection_sort(vector<int> &v){
	int len=v.size();
	for(int i=len-1; i>0; --i){
		//find max
		int max=INT_MIN, max_id=0;
		for(int j=0; j<=i; ++j){
			if(v[j]>max){
				max_id = j;
				max = v[j];
			}
		}
		swap(v,max_id,i);
	}
	return;
}
```

# 插入排序

```cpp
/*
 * insert sort
 */
void insert_sort(vector<int> &v){
	int len=v.size();
	int left=0, right;
	for(right=1; right<len; ++right){
		int j=right;
		int value = v[j];
		while(j>=1 && value<v[j-1]){
			v[j] = v[j-1];
			--j;
		}
		v[j] = value;
	}
	return;
}
```

# 归并排序

```cpp
/*
 * merge sort
 */
void merge_sort_recursive(vector<int>::iterator beg, vector<int>::iterator end){
	if(beg+1>=end)
		return;
	
	auto middle = beg + (end-beg)/2;	
	merge_sort_recursive(beg,middle);
	merge_sort_recursive(middle, end);
	
	vector<int> tmp_v(beg,end);
	auto t1 = tmp_v.begin();
	auto t2_end = tmp_v.end();
	auto t1_end =  t1 + (t2_end - t1)/2, t2 = t1_end;
	
	while(t1 != t1_end && t2 != t2_end){
		if(*t1 <= *t2){
			*beg = *t1;
			++t1;
		}else{
			*beg = *t2;
			++t2;	
		}
		++beg;
	}
	if(t1 == t1_end){
		t1 = t2;
		t1_end = t2_end;
	}
	while(t1 != t1_end){
		*beg = *t1;
		++t1;
		++beg;
	}
	
}

void merge_sort(vector<int> &v){
	merge_sort_recursive(v.begin(), v.end());
	return;
}
```



上面的代码实际上可以优化，主要问题在于每次迭代都重新申请一个数组，并且进行了复制。反复的申请空间可能造成额外的开销，可以一次申请一个同原数组一样的的临时数组，然后复用两个数组的空间。

```c++
//One temp array version
void merge_sort_recursive_t(vector<int>::iterator beg, vector<int>::iterator end, vector<int>::iterator t_beg, vector<int>::iterator t_end){
	if(beg+1>=end)
		return;
	
	auto middle = beg + (end-beg)/2;	
	auto t_middle = t_beg + (end-beg)/2;
	merge_sort_recursive_t(t_beg, t_middle, beg, middle);
	merge_sort_recursive_t(t_middle, t_end, middle, end);
	
	//merge
	auto t1 = beg;
	auto t2 = middle;
	while(t1 != middle && t2 != end){
		if(*t1 <= *t2)
			*t_beg = *(t1++);
		else
			*t_beg = *(t2++);
		++t_beg;
	}
	
	if(t1 == middle){
		t1 = t2;
		middle = end;
	}
	while(t1 != middle)
		*(t_beg++) = *(t1++);
	
	return;
}

void merge_sort(vector<int> &v){
	//merge_sort_recursive(v.begin(), v.end());
	vector<int> tmp(v.begin(), v.end());
	merge_sort_recursive_t(tmp.begin(), tmp.end(), v.begin(), v.end());
	return;
}
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

---

参考：

[https://stackoverflow.com/questions/9755721/how-can-building-a-heap-be-on-time-complexity](https://stackoverflow.com/questions/9755721/how-can-building-a-heap-be-on-time-complexity "如何在O\(N\)时间内建堆")

[https://zh.wikipedia.org/wiki/%E5%A0%86%E6%8E%92%E5%BA%8F](https://zh.wikipedia.org/wiki/堆排序 "Wiki.堆排序")

