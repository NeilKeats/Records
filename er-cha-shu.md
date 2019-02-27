# 1. 树的递归定义

树为节点集合，若节点非空，则有由一个根节点和0个或多个非空的（子）树组成，每个子树的根都被来自根r的一条有向边所连接。

**树叶**、**兄弟**、**父亲**、**祖先**、**后裔                                                  
**

**路径**：从节点n1到nk的path定义为节点n1,n2,n3,…,nk的一个序列：使得1&lt;=i&lt;k，ni是ni+1的父亲；路径长为边数目，即k-1

**深度**：根到ni的唯一路径的长，（根的深度为0）

**高度**：ni到一片树叶的最长路径的长。所有树叶高度为0，一棵树的高等于根的高

**内部路径长**：所有节点的深度。

# 2. 遍历二叉树（traversal）

## 2.1 BFS，队列

按层：往往要求获取层数信息（维护两个last变量，一个指向当前层，一个指向下一层）

[https://leetcode.com/problems/binary-tree-level-order-traversal](https://leetcode.com/problems/binary-tree-level-order-traversal)

## 2.2 DFS，栈

递归的函数上下文栈，或者循环+栈。递归简单清晰，应练习循环的遍历方法

* **先序遍历\(pre-order\)**：node, child left, child right

  * [https://leetcode.com/problems/binary-tree-preorder-traversal/](https://leetcode.com/problems/binary-tree-preorder-traversal/)

* **中序遍历**\(**in-order**\)：child left, node, child right

  * [https://leetcode.com/problems/binary-tree-inorder-traversal/](https://leetcode.com/problems/binary-tree-inorder-traversal/)

* **后序遍历**\(**post-order**\)：child left, child right, node

  * [https://leetcode.com/problems/binary-tree-postorder-traversal/](https://leetcode.com/problems/binary-tree-postorder-traversal/)

* **（先序）序列化**：

  * 加入空字符\#、结束字符！

* **反序列化**：

  * 由输入字符串-&gt;vector

  * "12!3!\#!\#!\#!" -&gt; \["12","3","\#","\#","\#"\]

# 3.二叉搜索树

* 树的任意点期望深度为`O(logN)`，平均操作复杂度为`O(logN)`。但删除操作会破坏这种平均特性，由于删除时，把右子树的最小节点代替原节点，有助于使得左子树比右子树更深。
* 已证明，交替插入删除`Θ(N^2)`次，期望深度将为`Θ(sqrt(N))`。
* 可通过交替选取右子树最小、左子树最大元素来代替被删节点来消除。
* 若向预先排序的树输入数据，一连串insert花费二次时间，链表实现代价很大，树由那些没有左儿子的节点组成。

**解决方法**：附加条件“平衡”

## 3.1 AVL树

> 带平衡条件的二叉查找树，保证树深度为`O(logN)`

**定义**：每个节点的左子树和右子树的高度最多差1的二叉查找树。（空树高度为-1）

**插入**可能破坏条件，通过“**旋转**”来修正。插入之后，只有插入点到根节点的平衡可能被改变（只有这些节点的子树可能发生改变），沿此路径上行并更新平衡信息。

### 3.1.1 **旋转操作**：

找出第一个破坏AVL条件的节点α（最深的节点），并重新平衡。

四种情况（WHY？，α的左右子树高度差4，若插在左/右儿子则高度差不可能为2，α不可能为第一个破坏点）：

1. 对α的左儿子的左子树插入
2. 对α的左儿子的右子树插入
3. 对α的右儿子的左子树插入（与2.对称）
4. 对α的右儿子的右子树插入（与1.对称）

通过单旋转、双旋转来解决。实现可**递归定义**插入、左/右的单/双旋转。

### 3.1.2 单旋转（左-左/右-右）

![](/assets/AVL_single_rotate.png)

### 3.1.2 双旋转（左-右/右-左）

![](/assets/AVL_double_rotate.png)

---

## 3.2 伸展树（Splay Tree）

> ## 放弃平衡条件，允许任意深度，但每次操作之后调整；
>
> 连续M次操作平均最坏情况`O(MlogN)`，无法保证任意一次操作花费`O(N)`的可能。
>
> **对于二叉查找树最坏情况**`O(N)`，若要求最差情况`O(MlogN)`则节点被访问之后必须被移动，否则一直访问最坏节点则有`O(MN)`

**基本想法**：当一个节点被访问后，经过一系列AVL树的旋转被构造到根节点。若该点很深，则路径上的许多存在节点也相对比较深，重新构造可以使得对这些节点的访问时间变少。（实际上，一个节点被访问之后，可能不久再次被访问）

不保留高度或平衡信息

### 3.2.1 平衡方法

直接单旋转，对最坏情况复杂度为`O(MN)`，因此不可行。

展开操作：X为目标节点，若其父为根节点，直接单旋转。否则分为以下情况

### 3.2.2 之字形（Zig-zag），即采用AVL的双旋转

![](/assets/Splay_Zigzag.png)

### 3.2.3 一字型（Zig-zig）

![](/assets/Splay_Zigzig.png)

---



