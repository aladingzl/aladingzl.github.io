---
layout:     post
title:      "斐波那契数列"
subtitle:   "How to solve the Fibonacci sequence？"
date:       2021-12-12
author:     "aladingzl"
header-style: text
tags:
    - algorithm
    - LeetCode
    - 笔记
---

[509. 斐波那契数](https://leetcode-cn.com/problems/fibonacci-number/)

该题在 LeetCode 上虽然是一道 easy，各种场景下也见了很多次，有时候没怎么分析就过去了，但后面做题发现其中的一些知识点还是值得总结一下的！

> **斐波那契数** （通常用 `F(n)` 表示）形成的序列称为 **斐波那契数列** 。该数列由 `0` 和 `1` 开始，后面的每一项数字都是前面两项数字的和。也就是：

```
F(0) = 0，F(1) = 1
F(n) = F(n - 1) + F(n - 2)，其中 n > 1
```

### 递归法

其实看到斐波那契的公式很自然的可以想到递归解法，下面直接给出代码实现：

```javascript
var fib = function(n) {
  	// 终止条件
    if (n == 0) return 0;
    if (n == 1) return 1;
  	// 向下求解
    return fib(n - 1) + fib(n - 2);
};
```

**时间复杂度分析**

其实递归求解过程就是构造一颗二叉树，深度为 k 的满二叉树有 2^k - 1 个节点，每次递归的操作数为 1，递归次数可以看成指数级 2^n 大小。

所以这个递归的时间复杂度为 **O(2^n)**

<img src="https://cdn.jsdelivr.net/gh/aladingzl/PicGoCDN//img/fib01.jpg" alt="fib01" style="zoom: 50%;" />

指数级复杂度太高，n 较大时显然是无法接受的，下面进行优化。

从上图可以发现存在很多重复节点，所以我们可以对已经计算出的结果进行相应的存储，下面给出代码实现：

```javascript
var fib = function(n) {
    const memo = {}; // 存储已有结果

    const helper = (x) => {
        if (memo[x]) return memo[x];
        if (x == 0) return 0;
        if (x == 1) return 1;
        memo[x] = fib(x - 1) + fib(x - 2);
        return memo[x];
    };

    return helper(n);
};
```

优化后相当于进行了剪枝操作，相同的节点仅执行了一次

### 动态规划

斐波那契数存在递推关系，可以使用动态规划求解。动态规划的状态转移方程即为上述递推关系，边界条件为 *F*(0) 和 *F*(1)。可以得到下面的代码实现：

```javascript
var fib = function(n) {
    if(n < 2) return n;
    const dp = [];
    dp[0] = 0, dp[1] = 1;
    for(let i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n];
};
```

**复杂度分析**

- 时间复杂度 **O(n)**
- 空间复杂度 **O(n)**

由于 *F*(*n*) 只和 *F*(*n*−1) 与 *F*(*n*−2) 有关，因此可以使用「滚动数组思想」把空间复杂度优化成 ***O*(1)**。

代码实现：

```javascript
var fib = function(n) {
    if (n < 2) {
        return n;
    }
    let p = 0, q = 0, r = 1;
    for (let i = 2; i <= n; i++) {
        p = q;
        q = r;
        r = p + q;
    }
    return r;
};
```

### 最后

果然算法的尽头是数学:smile:，官方题解给出了**矩阵快速幂**和**通项公式法**，属实强！给我看傻了。

最后最后打个表娱乐娱乐吧

```javascript
var fib = function(n) {
    const nums = [0,1,1,2,3,5,8,13,21,34,55,89,144,233,377,610,987,1597,2584,4181,6765,10946,17711,28657,46368,75025,121393,196418,317811,514229,832040];
    return nums[n];
};
```

