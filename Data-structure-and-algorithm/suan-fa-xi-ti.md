> wLeetcode & 剑指offer题

# 字符串

---

# 链表

## 反转链表

> OJ：[牛客](https://www.nowcoder.com/practice/75e878df47f24fdc9dc3e400ec6058ca?tpId=13&tqId=11168&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)， [Leetcode\[easy\]](https://leetcode.com/problems/reverse-linked-list/)

**非递归方法**

保存两个指针，一个`prev` 指针指向当前元素的前驱，一个当前指针`curr`（可用`pHead`代替）。用到临时指针`nex`用作交换。

时间复杂度：$$ O(N) $$

空间复杂度：$$ O(1) $$

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

**递归方法**

时间复杂度：$$ O(N) $$

空间复杂度：$$ O(1) $$ （？考虑到递归的栈开销，使用了尾递归，Leetcode的subbmission显示内存占用与跟非递归的基本一致，因此可以认为是$$ O(1) $$）

```cpp
class Solution {
private:
    ListNode *reverseList_recursively(ListNode *prev,ListNode *curr){
        if(!curr)
            return prev;
        ListNode *nxt = curr->next;
        curr->next = prev;

        return reverseList_recursively(curr, nxt);
    }

public:
    ListNode* reverseList(ListNode* head) {
        return reverseList_recursively(nullptr,head);
    }
};
```

> PS，显然这里没太大必要使用递归方法，毕竟还有额外的调用开销
>
> 上面的代码在Leetcode提交，递归版本运行时间为16ms，而非递归仅需要8ms

## 从尾到头输出链表

> OJ：[牛客](https://www.nowcoder.com/practice/d0267f7f55b3412ba93bd35cfa8e8035?tpId=13&tqId=11156&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

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

时间复杂度：$$ O(N) $$

空间复杂度：$$ O(N) $$，（因为栈深为N）

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

> 也有方法为使用`vector`，顺序存储后，再inverse（调用stl函数）

---

# 树

## 层序遍历

## 中序遍历

## 后序遍历

## 求二叉树的最大/最小深度

> OJ：[牛客](https://www.nowcoder.com/practice/435fb86331474282a3499955f0a41e8b?tpId=13&tqId=11191&tPage=2&rp=2&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)，[Leetcode\[easy\]](https://leetcode.com/problems/maximum-depth-of-binary-tree/submissions/)
>
> 求树的深度**需要遍历整棵树**，因为遍历完毕之前，总有可能存在存在一条未走完的路径比当前最长路径更长。
>
> 直观的想法是，直接遍历树，并维护一个最深的叶子的深度，不断更新。

实际上，由定义可知当前节点的深度`curr = 1 + max(left, right);`

**递归计算树的深度**（深度优先搜索DFS遍历方法）

时间复杂度：$$ O(N) $$，（由于遍历的缘故）

空间复杂度：$$ O(N) $$，（考虑最坏情况，树的节点只有左或右儿子，那么递归深度为N）

```cpp
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        if(!pRoot)
            return 0;
        int dep_l = TreeDepth(pRoot->left);
        int dep_r = TreeDepth(pRoot->right);
        return (1 + (dep_l>dep_r? dep_l : dep_r));
    }
};
```

此外，前文提到，可以直接遍历树，维护一个最深叶子深度。想一想，遍历方法中分为DFS和BFS，而DFS可以使用上述的递归方法求。对于BFS，实际上层序遍历可以通过队列实现，并且通常需要判断**换层**操作。那么只需要统计**换层**的次数，最后就可以知道整棵树的深度。

**非递归计算树的深度**（广度优先BFS遍历方法）

时间复杂度：$$ O(N) $$，（由于遍历的缘故）

空间复杂度：$$ O(N) $$，（考虑完全二叉树，最后一层或倒数第二层，队列中有$$ O(N/2) $$ 以及 $$ O(N/4) $$ 个节点）

```cpp
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        queue<TreeNode*> q;
        int deep= 0;
        TreeNode *last = pRoot, *nlast;
        if(pRoot)
            q.push(pRoot);
        while(!q.empty()){
            TreeNode *cur = q.front();
            q.pop();
            if(cur->left){
                q.push(cur->left);
                nlast = cur->left;
            }
            if(cur->right){
                q.push(cur->right);
                nlast = cur->right;
            }
            if(cur == last ){
                ++deep;
                last = nlast;
            }
        }
        return deep;
    }
};
```

---

# 其他



