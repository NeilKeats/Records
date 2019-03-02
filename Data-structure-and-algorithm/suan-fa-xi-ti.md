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



---

# 树

---

# 其他



