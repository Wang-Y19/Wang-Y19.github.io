---
layout: post
title: 打家劫舍解题思路总结
date: 2020-04-05
author: WangY.
tags: [动态规划, dp, 环形数组, 二叉树, Java]
comments: true
toc: true
---

本文参考自 LeetCode：https://leetcode-cn.com/problems/house-robber-ii/solution/tong-yong-si-lu-tuan-mie-da-jia-jie-she-wen-ti-by-/

LeetCode上的打家劫舍问题共有3道，分别是：[198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)，[213. 打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/)和[337. 打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/)。第一道是比较标准的动态规划问题，而第二道融入了环形数组的条件，第三道则把动态规划的自底向上和自顶向下解法和二叉树结合起来。

在刷题过程中发现了一种通用方法，用状态机的技巧来解决，可以解决上述所用问题，以不变应万变。

下面，我们从第一道开始分析。



## 一、House Robber I

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你**在不触动警报装置的情况下**，能够偷窃到的最高金额。

**示例 1:**

> 输入: [1,2,3,1]
>
> 输出: 4
>
> 解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。偷窃到的最高金额 = 1 + 3 = 4 。

```java
public int rob(int[] nums);
```

题目很容易理解，而且动态规划的特征很明显。上篇博文「股票问题解题思路总结」做过总结，解决动态规划问题就是找「状态」和「选择」，仅此而已。

假想你就是这个专业强盗，从左到右走过这一排房子，在每间房子前都有两种选择：抢或者不抢。

如果你抢了这间房子，那么你肯定不能抢相邻的下一间房子了，只能从下下间房子开始做选择。

如果你不抢这件房子，那么你可以走到下一间房子前，继续做选择。

当你走过了最后一间房子后，你就没得抢了，能抢到的钱显然是 0（base case）。

以上的逻辑很简单吧，其实已经明确了「状态」和「选择」：你面前房子的索引就是状态，抢和不抢就是选择。

在两个选择中，每次都选更大的结果，最后得到的就是最多能抢到的 money：
```java
// 主函数
public int rob(int[] nums) {
    return dp(nums, 0);
}
// 返回 nums[start..] 能抢到的最大值
private int dp(int[] nums, int start) {
    if (start >= nums.length) {
        return 0;
    }
    
    int res = Math.max(
            // 不抢，去下家
            dp(nums, start + 1), 
            // 抢，去下下家
            nums[start] + dp(nums, start + 2)
        );
    return res;
}
```

> 明确了状态转移，就可以发现对于同一 `start` 位置，是存在重叠子问题的，比如：(0表示不抢，1表示抢)
>
> case1:  010101
>
> case2：101001

盗贼有多种选择可以走到这个位置，如果每次到这都进入递归，岂不是浪费时间？所以说存在重叠子问题，可以用备忘录进行优化：
```java
private int[] memo;
// 主函数
public int rob(int[] nums) {
    // 初始化备忘录
    memo = new int[nums.length];
    Arrays.fill(memo, -1);
    // 强盗从第 0 间房子开始抢劫
    return dp(nums, 0);
}

// 返回 dp[start..] 能抢到的最大值
private int dp(int[] nums, int start) {
    if (start >= nums.length) {
        return 0;
    }
    // 避免重复计算
    if (memo[start] != -1) return memo[start];
    
    int res = Math.max(dp(nums, start + 1),  nums[start] + dp(nums, start + 2));
    
    // 记入备忘录
    memo[start] = res;
    return res;
}
```

这就是**自顶向下**的动态规划解法，我们也可以略作修改，写出**自底向上**的解法：
```java
int rob(int[] nums) {
    int n = nums.length;
    // dp[i] = x 表示：
    // 从第 i 间房子开始抢劫，最多能抢到的钱为 x
    // base case: dp[n] = 0
    int[] dp = new int[n + 2];
    for (int i = n - 1; i >= 0; i--) {
        dp[i] = Math.max(dp[i + 1], nums[i] + dp[i + 2]);
    }
    return dp[0];
}
```

我们又发现状态转移只和 `dp[i]` 最近的两个状态有关，所以可以进一步优化，将空间复杂度降低到 `O(1)`。
```java
//自顶向下
int rob(int[] nums) {
    int n = nums.length;
    int dp_i = 0, dp_i_1 = 0;
    int dp_i_2 = 0; 
    //注意，此处的dp_i_2是指第i天的最高金额。
    //因为根据状态转移方程，dp[i]与dp[i-1]和dp[i-2]有关
    //为了避免数组超限，初始化dp_i = 0和dp_i_1 = 0用来代替i=0时的dp[-2]和dp[-1]
    for (int i = 0; i < n; i++) {
        dp_i_2 = Math.max(dp_i_1, nums[i] + dp_i);
        dp_i = dp_i_1;
        dp_i_1 = dp_i_2;
    }
    return dp_i_2;
}
//自底向上
int rob(int[] nums) {
    int n = nums.length;
    // 记录 dp[i+1] 和 dp[i+2]
    int dp_i_1 = 0, dp_i_2 = 0;
    // 记录 dp[i]
    int dp_i = 0; 
    for (int i = n - 1; i >= 0; i--) {
        dp_i = Math.max(dp_i_1, nums[i] + dp_i_2);
        dp_i_2 = dp_i_1;
        dp_i_1 = dp_i;
    }
    return dp_i;
}
```



## 二、House Robber II

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方**所有的房屋都围成一圈**，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你在**不触动警报装置的情况下**，能够偷窃到的最高金额。

**示例 1:**

> 输入: [2,3,2]
>
> 输出: 3
>
> 解释: 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。

这道题目和第一道描述基本一样，强盗依然不能抢劫相邻的房子，输入依然是一个数组，但是告诉你这些房子不是一排，而是围成了一个圈。

也就是说，现在第一间房子和最后一间房子也相当于是相邻的，不能同时抢。比如说输入数组 nums=[2,3,2]，算法返回的结果应该是 3 而不是 4，因为开头和结尾不能同时被抢。

首先，首尾房间不能同时被抢，那么只可能有三种不同情况：要么都不被抢；要么第一间房子被抢最后一间不抢；要么最后一间房子被抢第一间不抢。

其实我们**不需要比较三种情况，只要比较情况二和情况三就行**，因为这两种情况对于房子的选择余地比情况一大呀，**房子里的钱数都是非负数，所以选择余地大，最优决策结果肯定不会小**。

所以只需对之前的解法稍作修改即可：
```java
public int rob(int[] nums) {
    if (nums.length == 0) {
        return 0;
    } else if (nums.length == 1) {
        return nums[0];
    }
    return Math.max(roob(Arrays.copyOfRange(nums,0,nums.length-1)), 
                    roob(Arrays.copyOfRange(nums,1,nums.length)));
}

// 计算最优结果
int roob(int[] nums) {
	//方法同一
    //自顶向下 或 自底向上
}
```




## 三、House Robber III

在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

**示例 1:**

> 输入: [3,2,3,null,3,null,1]
>
>      3
>
>  /    \
>
> 2       3
>
>      \         \ 
>
>    ​     3          1
>
> 输出: 7 
>
> 解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.

**示例 2:**

> 输入: [3,4,5,1,3,null,1]
>
> ​       3
>
> ​     /    \
>
>       4       5
>
>   /    \        \ 
>
> 1     3        1
>
> 输出: 9
>
> 解释: 小偷一晚能够盗取的最高金额 = 4 + 5 = 9.

整体的思路完全没变，还是做抢或者不抢的选择，去收益较大的选择。甚至我们可以直接按这个套路写出代码：
```java
Map<TreeNode, Integer> memo = new HashMap<>();
public int rob(TreeNode root) {
    if (root == null) return 0;
    // 利用备忘录消除重叠子问题
    if (memo.containsKey(root)) 
        return memo.get(root);
    // 抢，然后去下下家
    int do_it = root.val
        + (root.left == null ? 0 : rob(root.left.left) + rob(root.left.right))
        + (root.right == null ? 0 : rob(root.right.left) + rob(root.right.right));
    // 不抢，然后去下家
    int not_do = rob(root.left) + rob(root.right);
    
    int res = Math.max(do_it, not_do);
    memo.put(root, res);
    return res;
}
```

这道题就解决了，时间复杂度 `O(N)`，`N` 为数的节点数。

但是这道题让我觉得巧妙的点在于，还有更漂亮的解法：

每个节点可选择偷或者不偷两种状态，根据题目意思，相连节点不能一起偷

- 当前节点选择偷时，那么两个孩子节点就不能选择偷了
- 当前节点选择不偷时，两个孩子节点只需要拿最多的钱出来就行(两个孩子节点偷不偷没关系)

我们使用一个大小为 `2` 的数组来表示 `int[] res = new int[2]` ，0 代表不偷，1 代表偷

任何一个节点能偷到的最大钱的状态可以定义为：

1. 当前节点选择不偷：当前节点能偷到的最大钱数 = 左孩子能偷到的钱 + 右孩子能偷到的钱
2. 当前节点选择偷：当前节点能偷到的最大钱数 = 左孩子选择自己不偷时能得到的钱 + 右孩子选择不偷时能得到的钱 + 当前节点的钱数

表示为公式如下：
```java
root[0] = Math.max(rob(root.left)[0], rob(root.left)[1]) + Math.max(rob(root.right)[0], rob(root.right)[1])
root[1] = rob(root.left)[0] + rob(root.right)[0] + root.val;
```

最终代码为：

```java
int rob(TreeNode root) {
    int[] res = dp(root);
    return Math.max(res[0], res[1]);
}

//返回一个大小为2的数组dp，dp[0]表示不抢root的话，得到的最大钱数;dp[1]表示抢root的话，得到的最大钱数
int[] dp(TreeNode root) {
    if (root == null)
        return new int[]{0, 0};
    
    int[] left = dp(root.left);
    int[] right = dp(root.right);
    
    // 抢，下家就不能抢了
    int rob = root.val + left[0] + right[0];
    // 不抢，下家可抢可不抢，取决于收益大小
    int not_rob = Math.max(left[0], left[1])+ Math.max(right[0], right[1]);
    
    return new int[]{not_rob, rob};
}
```

时间复杂度 `O(N)`，空间复杂度只有递归函数堆栈所需的空间，不需要备忘录的额外空间。