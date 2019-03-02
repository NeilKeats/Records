> wLeetcode & 剑指offer题

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

时间复杂度：$$ O(N) $$

空间复杂度：$$O(1)$$

保存两个指针，一个previous指针指向当前元素的前驱，一个当前指针`curr`（可用`pHead`代替）。用到临时指针`nex`用作交换。

## 从尾到头输出链表

由于单向链表无法反向遍历，因此自然可以想到利用栈（先进后出）来解决。

栈的利用，可以通过递归调用来达到，也可以自己对栈进行操作。

**递归实现**

```cpp
class Solution {
private:
    void TraversalFromTail_Recursive(vector<int> &vi,ListNode* curr){
        if(!curr)
            return;
        TraversalFromTail_Recursive(vi, curr->next);
        vi.push_back(curr->val);
        return ;
    }
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        stack<ListNode*> stk;
        vector<int> arr;
        TraversalFromTail_Recursive(arr, head);
        return arr;
    }
};
```

**调用栈结构实现**

```cpp
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        stack<ListNode*> stk;
        vector<int> arr;
        while(head){
            stk.push(head);
            head = head->next;
        }
        while(!stk.empty()){
            head = stk.top();
            stk.pop();
            arr.push_back(head->val);
        }
        return arr;
    }
};
```

时间复杂度：$$ O(N) $$

空间复杂度：$$ O(N) $$，栈深为N

也有方法为使用`vector`，顺序存储后，再inverse（调用stl函数）

# 树

---

# 其他



