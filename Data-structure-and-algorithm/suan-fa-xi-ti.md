> Leetcode & 剑指offer题

# 字符串

---

# 链表

## 反转链表

```cpp
class Solution {
public:
    ListNode* ReverseList(ListNode* pHead) {
        ListNode *prev = nullptr, *curr = pHead, *nex;
        while(curr){
            nex = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nex;
        }
        return prev;
    }
};
```

保存两个指针，一个previous指针指向当前元素的前驱，一个当前指针`curr`（可用`pHead`代替）。用到临时指针`nex`用作交换。



---

# 树

---

# 其他



