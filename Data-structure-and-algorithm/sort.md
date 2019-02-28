> # 常见排序方法
>
> 冒泡、选择排序、插入排序、归并排序、快速排序

# 快速排序

```cpp
void quick_sort_recursive(vector<int>::iterator beg, vector<int>::iterator end){
	if(beg+1 >= end)
		return;
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



