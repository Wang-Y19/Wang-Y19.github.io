---
layout: post
title: LeetCode-Java题解
date: 2020-01-31
author: WangY.
tags: [Java, code, LeetCode]
comments: true
toc: true
---

目前正在准备实习面试，在LeetCode上刷相关题目。

此博客用于记录一些较难、经典的题目和一些巧妙的解法。

题目及部分代码来自[力扣](https://leetcode-cn.com/)，不断更新中……


## 丑数

### 题目介绍

编写一个程序，找出第 n 个丑数。

丑数就是只包含质因数 2, 3, 5 的正整数。

**示例:**

> 输入: n = 10
> 输出: 12
> 解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。

**说明:** 

1.    1 是丑数。
2.    n 不超过1690。



### 代码实现


```java
/**
 * 丑数求解过程：首先除2，直到不能整除为止，然后除5到不能整除为止，然后除3直到不能整除为止。
 * 最终判断剩余的数字是否为1，如果是1则为丑数，否则不是丑数
 * 
 * 解题思路：
 * 从1开始遍历，按丑数求解过程找出满足条件的第n个丑数（提交超时）
 * 思路优化（如何利用之前的计算）
 * 解题二：动态规划+三指针
 * dp保存按序排列的丑数，三指针分别是*2，*3，*5，找出下一个丑数
 *
 * @param n
 * @return
 */
public int nthUglyNumber(int n) {
    int[] dp = new int[n];
    dp[0] = 1;
    int i2 = 0, i3 = 0, i5 = 0;
    for (int i = 1; i < n; i++) {
        int min = Math.min(dp[i2] * 2, Math.min(dp[i3] * 3, dp[i5] * 5));
        if (min == dp[i2] * 2) i2++;
        if (min == dp[i3] * 3) i3++;
        if (min == dp[i5] * 5) i5++;
        dp[i] = min;
    }

    return dp[n - 1];
}
```



### 笔记

三指针法。一部分是丑数数组，另一部分是权重2，3，5。下一个丑数，定义为丑数数组中的数乘以权重，所得的最小值。

那么，2该乘以谁？3该乘以谁？5该乘以谁？

其一，使用三个指针`idx[3]`，告诉它们。比如，2应该乘以`ugly[idx[0]]`，即数组中的第`idx[0]`个值。（权重2，3，5分别对应指针，idx[0],idx[1],idx[2]）

其二，当命中下一个丑数时，说明该指针指向的丑数 乘以对应权重所得积最小。此时，指针应该指向下一个丑数。（`idx[]`中保存的是丑数数组下标）

其三，要使用三个并列的`if`让指针指向一个更大的数，不能用`if-else`。因为有这种情况：

- 丑数6，可能由于丑数2乘以权重3产生；也可能由于丑数3乘以权重2产生。
- 丑数10，... 等等。



## 二叉树的遍历

### 前序遍历题目介绍

给定一个二叉树，返回它的前序遍历。

（前序遍历：先访问根节点——左子树——右子树。）

**示例:**

> 输入: [1,null,2,3]
>    1
>       \
>         2
>       /
>    3
> 输出: [1,2,3]



### 前序遍历代码实现

**迭代法：**

从根节点开始，每次迭代弹出当前栈顶元素，并将其孩子节点压入栈中，先压右孩子再压左孩子。

在这个算法中，输出到最终结果的顺序按照 `Top->Bottom` 和 `Left->Right`，符合前序遍历的顺序。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
  public List<Integer> preorderTraversal(TreeNode root) {
    LinkedList<TreeNode> stack = new LinkedList<>();
    LinkedList<Integer> output = new LinkedList<>();
    if (root == null) {
      return output;
    }

    stack.add(root);
    while (!stack.isEmpty()) {
      //检索并弹出stcak中最后一个元素
      TreeNode node = stack.pollLast(); 
      //将弹出的元素加入output中
      output.add(node.val);
      
      if (node.right != null) {
        stack.add(node.right);
      }
      if (node.left != null) {
        stack.add(node.left);
      }
        
    }
    return output;
  }
}
```



**算法复杂度：**

- 时间复杂度：访问每个节点恰好一次，时间复杂度为*O*(*N*)，其中*N*是节点的个数，也就是树的大小。
- 空间复杂度：取决于树的结构，最坏情况存储整棵树，因此空间复杂度是*O*(*N*)。



### 中序遍历题目介绍

给定一个二叉树，返回它的中序遍历。

（中序遍历：先访问左子树——根节点——右子树。）

**示例:**

> 输入: [1,null,2,3]
>    1
>       \
>         2
>       /
>    3
> 输出: [1,3,2]



### 中序遍历代码实现

解法一：**递归**

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List <Integer> inorderTraversal(TreeNode root) {
        List <Integer> res = new ArrayList <> ();
        helper(root, res);
        return res;
    }

	public void helper(TreeNode root, List <Integer> res) {
    	if (root != null) {
       	   if (root.left != null) {
           	  helper(root.left, res);
       	   }
           res.add(root.val);
           if (root.right != null) {
              helper(root.right, res);
           }
        }
    }
}
```



**算法复杂度：**

- 时间复杂度：*O*(*n*)。递归函数 *T*(*n*) = 2 · *T*(*n*/2)+1。
- 空间复杂度：最坏情况下需要空间*O*(*n*)，平均情况为*O*(*logn*)。





解法二：**基于栈的中序遍历**

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public List <Integer> inorderTraversal(TreeNode root) {
        List <Integer> res = new ArrayList <> ();
        Stack <TreeNode> stack = new Stack <> ();
        TreeNode curr = root;
        while (curr != null || !stack.isEmpty()) {
            while (curr != null) {
                stack.push(curr);
                curr = curr.left;
            }
            curr = stack.pop();
            res.add(curr.val);
            curr = curr.right;
        }
        return res;
    }
}
```



**算法复杂度：**

- 时间复杂度：*O*(*n*)。
- 空间复杂度：*O*(*n*)。



### 笔记

