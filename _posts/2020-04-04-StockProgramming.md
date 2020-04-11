---
layout: post
title: 股票问题解题思路总结
date: 2020-04-04
author: WangY.
tags: [动态规划, dp, Java]
comments: true
toc: true
---

本文参考自英文版 LeetCode：https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/discuss/108870/Most-consistent-ways-of-dealing-with-the-series-of-stock-problems

LeetCode上的股票问题共有6道，分别是：[121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)，[122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)，[309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)，[714. 买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)，[123. 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)和[188. 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)。

在刷题过程中发现了一种通用方法，用状态机的技巧来解决，可以解决上述所用问题，以不变应万变。

这 6 道股票买卖问题是有共性的，我们通过对最后一题（限制最大交易次数为 k）的分析一道一道解决。因为IV是一个最泛化的形式，其他的问题都是这个形式的简化。

第一题是只进行一次交易，相当于 k = 1；II是不限交易次数，相当于 k = +infinity（正无穷）；III是只进行 2 次交易，相当于 k = 2；剩下两道也是不限次数，但是加了交易「冷冻期」和「手续费」的额外条件，其实就是II的变种，都很容易处理。



## 一、穷举框架

**利用「状态」进行穷举**：具体到每一天，看看总共有几种可能的「状态」，再找出每个「状态」对应的「选择」。要穷举所有「状态」，穷举的目的是根据对应的「选择」更新状态。

在股票问题中，每天都有三种「选择」：买入、卖出、无操作，我们用 buy, sell, rest 表示这三种选择。但问题是，并不是每天都可以任意选择这三种选择的，因为 sell 必须在 buy 之后，buy 必须在 sell 之后。那么 rest 操作还应该分两种状态，一种是 buy 之后的 rest（持有了股票），一种是 sell 之后的 rest（没有持有股票）。而且别忘了，我们还有交易次数 k 的限制，就是说你 buy 还只能在 k > 0 的前提下操作。

> 其实题目对股票的买卖进行了简化：
>
> 题目规定：如果在第 1 天和第 2 天接连购买股票，之后再将它们卖出，属于同时参与了多笔交易。
>
> 即，在一次买入后，下一次一定是卖出，此时一笔交易结束。

本问题的「状态」有三个，第一个是天数，第二个是允许交易的最大次数，第三个是当前的持有状态（即之前说的 rest 的状态，我们不妨用 1 表示持有，0 表示没有持有）。然后我们用一个三维数组就可以装下这几种状态的全部组合：
```java
dp[i][k][0 or 1]
0 <= i <= n-1, 1 <= k <= K
n 为天数，大 K 为最多交易数
此问题共 n × K × 2 种状态，全部穷举就能搞定。

for 0 <= i < n:
    for 1 <= k <= K:
        for s in {0, 1}:
            dp[i][k][s] = max(buy, sell, rest)
```

我们可以用自然语言描述出每一个状态的含义，比如说 `dp[3][2][1]` 的含义就是：今天是第四天，至今最多进行了 2 次交易，手上持有股票。再比如 `dp[2][3][0]` 的含义：今天是第三天，至今最多进行了 3 次交易，手上没有持有股票。

我们想求的最终答案是 `dp[n - 1][K][0]`，即最后一天，最多允许 K 次交易，最多获得多少利润。读者可能问为什么不是 `dp[n - 1][K][1]`？因为 [1] 代表手上还持有股票，[0] 表示手上的股票已经卖出去了，很显然后者得到的利润一定大于前者。




## 二、状态转移框架

**状态转移方程：**
```java
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
              max(   选择 rest  ,           选择 sell      )

解释：今天我没有持有股票，有两种可能：
要么是我昨天就没有持有，然后今天选择 rest，所以我今天还是没有持有；
要么是我昨天持有股票，但是今天我 sell 了，所以我今天没有持有股票了。

dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
              max(   选择 rest  ,           选择 buy         )

解释：今天我持有着股票，有两种可能：
要么我昨天就持有着股票，然后今天选择 rest，所以我今天还持有着股票；
要么我昨天本没有持有，但今天我选择 buy，所以今天我就持有股票了。
```

如果 buy，就要从利润中减去 `prices[i]`，如果 sell，就要给利润增加 `prices[i]`。今天的最大利润就是这两种可能选择中较大的那个。

注意 `k` 的限制，我们在选择 buy 的时候，把 `k` 增加 1，因为根据题目的限制：`买入+卖出 = 完成一笔交易`。当然也可以在 sell 的时候减加1，一样的。

**最后是定义 base case，即最简单的情况：**
```java
dp[-1][k][0] = 0
解释：因为 i 是从 0 开始的，所以 i = -1 意味着还没有开始，这时候的利润当然是 0 。
dp[-1][k][1] = -infinity
解释：还没开始的时候，是不可能持有股票的，用负无穷表示这种不可能。

dp[i][0][0] = 0
解释：因为 k 是从 1 开始的，所以 k = 0 意味着根本不允许交易，这时候利润当然是 0 。
dp[i][0][1] = -infinity
解释：不允许交易的情况下，是不可能持有股票的，用负无穷表示这种不可能。
```

**总结：**
```java
base case：
dp[-1][k][0] = dp[i][0][0] = 0
dp[-1][k][1] = dp[i][0][1] = -infinity

状态转移方程：
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
```



## 三、题目分析与解答

### 1.买卖股票的最佳时机

给定一个数组，它的第 `i` 个元素是一支给定股票第 `i` 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。

注意：你不能在买入股票前卖出股票。

**示例:**

> 输入: [7,1,5,3,6,4]
>
> 输出: 5
>
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
>
> 注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。

**解题思路：k = 1**

直接套状态转移方程，根据 base case，可以做一些化简：

```java
dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])
dp[i][1][1] = max(dp[i-1][1][1], dp[i-1][0][0] - prices[i]) 
            = max(dp[i-1][1][1], -prices[i]) 
解释：因为只会交易一次，所以在买入前一定不会持有，所以 dp[i-1][0][0] = 0。

现在发现 k 都是 1，不会改变，即 k 对状态转移已经没有影响了。
可以进行进一步化简去掉所有 k：
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], -prices[i])
```

当`i=0`时，状态转移方程中`dp[i-1] = dp[-1]`是不合法的，应当对`i`的 base case 进行处理：

`dp[i][0] = 0`，`dp[i][1] = -prices[i]`

但是这样处理 base case 很麻烦，而且注意一下状态转移方程，新状态只和相邻的一个状态有关，其实不用整个 `dp` 数组，只需要一个变量储存相邻的那个状态就足够了，这样可以把空间复杂度降到 `O(1)`，代码实现如下：

**代码实现：**

```java
public class Solution {
    public int maxProfit(int prices[]) {
        int n = prices.length;
        // base case: dp[-1][0] = 0, dp[-1][1] = -infinity
        int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
        for(int i = 0; i < n; i++){
            // dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
            dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
            // dp[i][1] = max(dp[i-1][1], -prices[i])
            dp_i_1 = Math.max(dp_i_1, -prices[i]);
        }
        return dp_i_0;
    }
}
```



### 2.买卖股票的最佳时机 II

给定一个数组，它的第 `i` 个元素是一支给定股票第 `i` 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例:**

> 输入: [7,1,5,3,6,4]
>
> 输出: 7
>
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。

**解题思路：k = +infinity**

如果 k 为正无穷，那么就可以认为 k 和 k - 1 是一样的。可以这样改写框架：
```java
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
            = max(dp[i-1][k][1], dp[i-1][k][0] - prices[i])

我们发现数组中的 k 已经不会改变了，也就是说不需要记录 k 这个状态了：
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i])
```

**代码实现：**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
        for (int i = 0; i < n; i++) {
            int temp = dp_i_0; //存储前一天的状态
            dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
            dp_i_1 = Math.max(dp_i_1, temp - prices[i]);
        }
        return dp_i_0;
    }
}
```



### 3.最佳买卖股票时机含冷冻期

给定一个整数数组，其中第 `i` 个元素代表了第 `i` 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

- 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**示例 1:**


> 输入: [1,2,3,0,2]
> 输出: 3 
> 解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]

**解题思路：k = +infinity with cooldown**

每次 sell 之后要等一天才能继续交易。只要把这个特点融入上一题的状态转移方程即可：
```java
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-2][0] - prices[i])
解释：第 i 天选择 buy 的时候，要从 i-2 的状态转移，而不是 i-1 。
```

> 假设第`i-1`天没有卖出股票，为什么第`i`天不能买`i-1`天的股票？
>
> 因为前面的状态对后面会有影响，我每次持有股票，都保证自己的利润最大。对于第`i`天，如果存在第`i-1`天卖出股票就获得最大利润的情况，那么对于第`i`天也会是这个利润值。如果不是，在第`i-1`天卖掉股票就会更新这个最大值。 所以会有`max`这个比较，就是时刻保证此时`0，1`状态的利润均为最大值。

**代码实现：**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
        // 代表 dp[i-2][0]
        int dp_pre_0 = 0;
        for(int i = 0; i < n; i++){
            int temp = dp_i_0; //存储前一天的状态（i-1）
            dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
            dp_i_1 = Math.max(dp_i_1, dp_pre_0 - prices[i]);
            dp_pre_0 = temp; //因为i++，所以此时的i-2，等于一天前的i-1
        }
        return dp_i_0;
    }
}
```



### 4.买卖股票的最佳时机含手续费

给定一个整数数组 `prices`，其中第 `i` 个元素代表了第 `i` 天的股票价格 ；非负整数 `fee` 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每次交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

**示例:**

> 输入: prices = [1, 3, 2, 8, 4, 9], fee = 2
>
> 输出: 8
>
>   解释: 能够达到的最大利润:  
>
>   在此处买入 prices[0] = 1
>
>   在此处卖出 prices[3] = 8
>
> 在此处买入 prices[4] = 4
>
> 在此处卖出 prices[5] = 9
>
> 总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.

**解题思路：k = +infinity with fee**

每次交易要支付手续费，只要把手续费从利润中减去即可。改写方程：
```java
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i] - fee)
解释：相当于买入股票的价格升高了。
在第一个式子里减也是一样的，相当于卖出股票的价格减小了。
```

**代码实现：** 

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int n = prices.length;
        int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
        for(int i = 0; i < n; i++){
            int temp = dp_i_0;
            dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
            dp_i_1 = Math.max(dp_i_1, temp - prices[i] - fee);
        }
        return dp_i_0;
    }
}
```



### 5.买卖股票的最佳时机 III

给定一个数组，它的第 `i` 个元素是一支给定的股票在第 `i` 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例 1:**

> 输入: [3,3,5,0,0,3,1,4]
>
> 输出: 6
>
> 解释: 在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。

**解题思路：k=2**

k = 2 和前面题目的情况稍微不同，因为上面的情况都和 k 的关系不太大。要么 k 是正无穷，状态转移和 k 没关系了；要么 k = 1，跟 k = 0 这个 base case 挨得近，最后也没有存在感。

这道题 k = 2 和后面要讲的 k 是任意正整数的情况中，对 k 的处理就凸显出来了。
```java
原始的动态转移方程，没有可化简的地方
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
```

我们必须穷举所有状态。其实我们之前的解法，都在穷举所有状态，只是之前的题目中 k 都被化简掉了。这道题由于没有消掉 k 的影响，所以必须要对 k 进行穷举：
```java
int max_k = 2;
int[][][] dp = new int[n][max_k + 1][2];
for (int i = 0; i < n; i++) {
    for (int k = max_k; k >= 1; k--) {
        if (i - 1 == -1) { 
            /*处理 base case */ 
        }
        dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i]);
        dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i]);
    }
}
// 穷举了 n × max_k × 2 个状态。
return dp[n - 1][max_k][0];
```

这里 k 取值范围比较小，所以可以不用 for 循环，直接把 k = 1 和 2 的情况手动列举出来也可以：（从下往上阅读）

- `dp[i][2][0] = max(dp[i-1][2][0], dp[i-1][2][1] + prices[i])`（要么没有，要么把第二次买的卖出（第二笔交易完成））
- `dp[i][2][1] = max(dp[i-1][2][1], dp[i-1][1][0] - prices[i])`（要么持有，要么第二次购买（k由1变为2））
- `dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])`（要么没有，要么把第一次买的卖出（第一笔交易完成））
- `dp[i][1][1] = max(dp[i-1][1][1], -prices[i])`(要么持有，要么第一次购买（k由0变为1）)

**代码实现：**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        int dp_i1_0 = 0, dp_i1_1 = Integer.MIN_VALUE;
        int dp_i2_0 = 0, dp_i2_1 = Integer.MIN_VALUE;
        for(int i = 0; i < n; i++){
            dp_i1_0 = Math.max(dp_i1_0, dp_i1_1 + prices[i]);
            dp_i1_1 = Math.max(dp_i1_1, -prices[i]);
            dp_i2_0 = Math.max(dp_i2_0, dp_i2_1 + prices[i]);
            dp_i2_1 = Math.max(dp_i2_1, dp_i1_0 - prices[i]);
        }
        return dp_i2_0;
    }
}
```



### 6.买卖股票的最佳时机 IV

给定一个数组，它的第 `i` 个元素是一支给定的股票在第 `i` 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 `k` 笔交易。

注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例:**

> 输入: [2,4,1], k = 2
>
> 输出: 2
>
> 解释: 在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。

**解题思路：k = any integer**

有了上一题 `k = 2` 的铺垫，这题应该和上一题的第一个解法没啥区别。但是出现了一个超内存的错误，原来是传入的 `k` 值会非常大，`dp` 数组太大了。

一次交易由买入和卖出构成，至少需要两天。所以说有效的限制 `k` 应该不超过 `n/2`，如果超过，就没有约束作用了，相当于 `k = +infinity`。这种情况是之前解决过的：2.买卖股票的最佳时机 II

**代码实现：**

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int n = prices.length;
        if(k > n / 2){
            return maxProfitk(prices); //防止出现超内存
        }

        int[][][] dp = new int[n][k + 1][2];
        for (int i = 0; i < n; i++) {
            for (int j = k; j >= 1; j--) {
                //处理 base case
                if (i == 0) { 
                    dp[i][j][0] = 0; 
                    dp[i][j][1] = -prices[i];
                }else{
                    dp[i][j][0] = Math.max(dp[i-1][j][0], dp[i-1][j][1] + prices[i]);
                    dp[i][j][1] = Math.max(dp[i-1][j][1], dp[i-1][j-1][0] - prices[i]);
                }    
            }
        }
        return dp[n - 1][k][0];
    }
    
	//第二题代码（不限制交易次数的情况）
    private static int maxProfitk(int[] prices) {
        int n = prices.length;
        int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
        for (int i = 0; i < n; i++) {
            int temp = dp_i_0;
            dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
            dp_i_1 = Math.max(dp_i_1, temp - prices[i]);
        }
        return dp_i_0;
    }
}
```



## 四、总结

本文给大家讲了如何通过状态转移的方法解决复杂的问题，用一个状态转移方程秒杀了 6 道股票买卖问题。

关键就在于列举出所有可能的「状态」，然后想想怎么穷举更新这些「状态」。一般用一个多维 `dp` 数组储存这些状态，从 base case 开始向后推进，推进到最后的状态，就是我们想要的答案。

具体到股票买卖问题，我们发现了三个状态，使用了一个三维数组，无非还是穷举 + 更新。
