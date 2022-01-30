---
layout:     post
title:      "二叉树遍历"
subtitle:   "Binary tree traversal"
date:       2021-09-12
author:     "aladingzl"
header-style: text
tags:
    - algorithm
    - LeetCode
    - 笔记
---

### 二叉树前序遍历



### 二叉树中序遍历

**递归**

```javascript
var inorderTraversal = function(root) {
    const res = [];
    const inorder = (root) => {
        if (!root) {
            return;
        }
        inorder(root.left);
        res.push(root.val);
        inorder(root.right);
    }
    inorder(root);
    return res;
};
```

**迭代**

```javascript
var inorderTraversal = function(root) {
    const res = [];
    const stk = [];
    while (root || stk.length) {
        while (root) {
            stk.push(root);
            root = root.left;
        }
        root = stk.pop();
        res.push(root.val);
        root = root.right;
    }
    return res;
};
```





**Morris**

```javascript
var inorderTraversal = function (root) {
  const res = [];
  let predecessor = null;

  while (root) {
    if (root.left) {
      // predecessor 节点就是当前 root 节点向左走一步，然后一直向右走至无法走为止
      predecessor = root.left;
      while (predecessor.right && predecessor.right !== root) {
        predecessor = predecessor.right;
      }

      // 让 predecessor 的右指针指向 root，继续遍历左子树
      if (!predecessor.right) {
        predecessor.right = root;
        root = root.left;
      }
      // 说明左子树已经访问完了，我们需要断开链接
      else {
        res.push(root.val);
        predecessor.right = null;
        root = root.right;
      }
    }
    // 如果没有左孩子，则直接访问右孩子
    else {
      res.push(root.val);
      root = root.right;
    }
  }

  return res;
};
```



### 二叉树后序遍历
