---
title: 二叉树基础
created: 2026-05-13
updated: 2026-05-13
tags: [数据结构, 二叉树, 遍历]
source_dir: 数据结构与算法/树
source_files: [二叉树基础.md]
---

# 二叉树基础

## 核心概念

二叉树是由n（n≥0）个节点组成的有限集合，要么是空树，要么由一个根节点和两棵互不相交的左子树、右子树组成，每个节点最多只有两个"孩子"。

### 关键术语

| 术语 | 定义 |
|------|------|
| 根节点 | 树的起点，无父节点 |
| 叶子节点 | 无孩子的节点 |
| 节点的度 | 孩子的数量（二叉树中最多为2） |
| 深度 | 从根到当前节点的步数（根深度=1） |
| 高度 | 从当前节点到最远叶子的步数（叶子高度=0） |

## 三种常见形态

### 1. 满二叉树
- 除了叶子节点，每个节点都有两个子节点
- 每一层节点数达到最大值
- 深度为h的满二叉树节点数：`2^h - 1`

### 2. 完全二叉树
- 除最后一层，每层节点数达到最大值
- 最后一层节点集中在左侧，中间无空缺
- **典型应用**：堆的底层结构
- **存储优势**：可用数组顺序存储

### 3. 普通二叉树
- 不满足上述条件，节点分布无规律

## 五大核心性质

1. **性质1**：第i层最多有 `2^(i-1)` 个节点
2. **性质2**：深度h的二叉树最多有 `2^h - 1` 个节点
3. **性质3**：叶子节点数 n₀ = 度为2的节点数 n₂ + 1 ⭐（面试高频）
4. **性质4**：n个节点的完全二叉树深度 `h = ⌊log₂n⌋ + 1`
5. **性质5**：完全二叉树节点编号规则（数组存储基础）
   - 节点i的父节点：`⌊i/2⌋`
   - 左孩子：`2i`
   - 右孩子：`2i+1`

## 四种遍历方式

### 遍历分类

| 遍历类型 | 顺序 | 实现方式 | 典型应用 |
|----------|------|----------|----------|
| 前序遍历 | 根→左→右 | DFS/栈 | 构建树、复制树 |
| 中序遍历 | 左→根→右 | DFS/栈 | BST得到升序序列 ⭐ |
| 后序遍历 | 左→右→根 | DFS/栈 | 删除树、求深度 |
| 层序遍历 | 上→下，左→右 | BFS/队列 | 求层数、判断完全二叉树 |

### 递归模板

```java
// 前序遍历
public void preorderTraversal(TreeNode root) {
    if (root == null) return;
    System.out.print(root.val + " ");
    preorderTraversal(root.left);
    preorderTraversal(root.right);
}

// 中序遍历
public void inorderTraversal(TreeNode root) {
    if (root == null) return;
    inorderTraversal(root.left);
    System.out.print(root.val + " ");
    inorderTraversal(root.right);
}

// 后序遍历
public void postorderTraversal(TreeNode root) {
    if (root == null) return;
    postorderTraversal(root.left);
    postorderTraversal(root.right);
    System.out.print(root.val + " ");
}

// 层序遍历
public void levelOrder(TreeNode root) {
    if (root == null) return;
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        System.out.print(node.val + " ");
        if (node.left != null) queue.offer(node.left);
        if (node.right != null) queue.offer(node.right);
    }
}
```

## 存储结构

### 链式存储（推荐）
```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
}
```
- 适合各种形态
- 插入删除便捷

### 顺序存储（数组）
- 适配完全二叉树
- 通过索引快速定位父子节点
- 非完全二叉树空间浪费严重

## 高频面试题型

### 1. 求最大深度
```java
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}
```

### 2. 判断两棵树是否相同
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (p == null || q == null) return false;
    if (p.val != q.val) return false;
    return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
}
```

### 3. 最近公共祖先（LCA）⭐
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) return root;
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    if (left == null) return right;
    if (right == null) return left;
    return root; // 左右都找到，当前节点就是LCA
}
```

## 新手常见误区

| 误区 | 正解 |
|------|------|
| 混淆深度和高度 | 深度从根向下，高度从叶子向上 |
| 混淆前中后序顺序 | "先中后"指根的访问时机，左右永远是左先 |
| 递归忘记终止条件 | 必须处理 `root == null` |
| 层序用栈实现 | 层序必须用队列，栈是DFS |
| 二叉树节点度都是2 | 度可以是0/1/2 |
| 忽视空树 | 空树是合法二叉树，必须处理 |

## 与Linux内核的联系

- [[concepts/cfs-scheduler]] 使用红黑树组织进程，红黑树是二叉树的进阶形态
- 二叉树遍历的递归思想在内核代码中广泛使用

---

**学习路径**：二叉树 → [[concepts/red-black-tree]] → B树 → 文件系统索引