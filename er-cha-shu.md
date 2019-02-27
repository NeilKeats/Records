# 1. 树的递归定义

树为节点集合，若节点非空，则有由一个根节点和0个或多个非空的（子）树组成，每个子树的根都被来自根r的一条有向边所连接。

**树叶**、**兄弟**、**父亲**、**祖先**、**后移      
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

递归的函数上下文栈，或者循环+栈

* **先序遍历\(pre-order\)**：node, child left, child right
* **中序遍历**\(in-order\)：child left, node, child right
* **后序遍历**\(post-order\)：child left, child right, node



* **（先序）序列化**：
  *  加入空字符\#、结束字符！
* **反序列化**：
  * 由输入字符串-&gt;vector

  * "12!3!\#!\#!\#!" -&gt; \["12","3","\#","\#","\#"\\]



