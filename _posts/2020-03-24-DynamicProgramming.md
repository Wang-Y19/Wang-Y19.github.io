---
layout: post
title: 动态规划练习
date: 2020-03-24
author: WangY.
tags: [动态规划, dp, Java]
comments: true
toc: true
---

动态规划实际上是一类题目的总称，并不是指某个固定的算法。

动态规划的意义就是通过采用**递推（或者分而治之）**的策略，通过解决大问题的子问题从而解决整体的做法。

动态规划的**核心思想**是巧妙的将问题拆分成多个子问题，通过计算子问题而得到整体问题的解。而子问题又可以拆分成更多的子问题，从而用类似递推迭代的方法解决要求的问题。

不断更新中……



## 动态规划的解题核心

动态规划的解题核心主要分为两步：

1. **第一步：状态的定义**
2. **第二步：状态转移方程的定义**

在这里，我们为了避免混淆用“状态”这个词来替代“问题”这个词。“问题”表示的含义类似：题目、要求解的内容、题干中的疑问句这样的概念。状态表示我们在求解问题之中对问题的分析转化。

### 第一步：状态的定义

有的问题过于抽象，或者过于啰嗦干扰我们解题的思路，我们要做的就是将题干中的问题进行转化（换一种说法，含义不变）。转化成一系列同类问题的某个解的情况，比如说：

> 题目：求一个数列中最大连续子序列的和。

我们要将这个原问题转化为：

> 状态定义：Fk是第k项前的最大序列和，求F1～FN中最大值。

通过换一种表述方式，我们清晰的发现了解决问题的思路，如何求出F1～FN中的最大值是解决原问题的关键部分。上述将原问题转化成另一种表述方式的过程叫做：状态的定义。这样的状态定义给出了一种类似通解的思路，把一个原来毫无头绪的问题转换成了可以求解的问题。

### 第二步：状态转移方程的定义

在进行了状态的定义后，自然而然的想到去求解F1～FN中最大值。这也是状态定义的作用，让我们把一个总体的问题转化成一系列问题，而第二步：状态转移方程的定义则告诉我们如何去求解一个问题，对于上述已经转换成一系列问题我们要关注的点就在于：如何能够用前一项或者前几项的信息得到下一项，这种从最优子状态转换为下一个最优状态的思路就是动态规划的核心。 

对于上面的例子题目来说，状态转移方程的定义应该是：

> Fk=max{Fk-1+Ak,Ak} 
> Fk是前k项的和，Ak是第k项的值

仔细思考一番，我们能够得到这样的结论，对于前k个项的最大子序列和是前k-1项的最大子序列和Fk与第k项的和、或者第k项两者中较大的。这种状态转移的思路就是DP的核心。



## 动态规划的应用场景


看过了如何去使用动态规划，那么随之而来的问题就在于：既然动态规划不是某个固定的算法，而是一种策略思路，那么什么时候应该去使用什么时候不能用呢？答案很简短：

对于一个可拆分问题中存在可以由前若干项计算当前项的问题可以由动态规划计算。 



## 动态规划题目分析与解答

### 1.[最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例:**

> 输入: [-2,1,-3,4,-1,2,1,-5,4],
>
> 输出: 6
>
> 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

**解题思路：**

- 动态规划——首先对数组进行遍历，当前最大连续子序列和为 `sum`，结果为 `ans`
- 如果 `sum > 0`，则说明 `sum` 对结果有增益效果，则 `sum` 保留并加上当前遍历数字
- 如果 `sum <= 0`，则说明 `sum` 对结果无增益效果，需要舍弃，则 `sum` 直接更新为当前遍历数字
- 每次比较 `sum` 和 `ans`的大小，将最大值置为`ans`，遍历结束返回结果
- 时间复杂度：*O*(*n*)

**代码实现：**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int ans = nums[0];
        int sum = 0;
        for(int num : nums){
            if(sum > 0){
                sum += num;
            }else{
                sum = num;
            }
            ans = Math.max(sum, ans);
        }
        return ans;
    }
}
```



### 2.[不同路径](https://leetcode-cn.com/problems/unique-paths/)

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

问总共有多少条不同的路径？

**示例:**

> 输入: m = 3, n = 2
>
> 输出: 3
>
> 解释:
>
> 从左上角开始，总共有 3 条路径可以到达右下角。
>
> 1. 向右 -> 向右 -> 向下
> 2. 向右 -> 向下 -> 向右
> 3. 向下 -> 向右 -> 向右
>

**解题思路：** **动态规划**

令 `dp[i][j]`是到达 `i`, `j` 最多可能路径数。

动态方程：`dp[i][j] = dp[i-1][j] + dp[i][j-1]`

注意，对于第一行 `dp[0][j]`，或者第一列 `dp[i][0]`，由于都是在边界，所以只有 `1`条可能路径

时间复杂度：*O(m∗n)*

空间复杂度：*O(m∗n)*

**代码实现：**
```java
class Solution {
    public int uniquePaths(int m, int n) {
        int dp[][] = new int[m][n];
        for(int i = 0; i < m; i++) dp[i][0] = 1;
        for(int i = 0; i < n; i++) dp[0][i] = 1;
        for(int i = 1; i < m; i++){
            for(int j = 1; j < n; j++){
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
}
```



### 3.[不同路径 II](https://leetcode-cn.com/problems/unique-paths-ii/)

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

**示例 1:**

> 输入:
>
> [
>
>   [0,0,0],
>
>   [0,1,0],
>
>   [0,0,0]
>
> ]
>
> 输出: 2
>
> 解释:
>
> 3x3 网格的正中间有一个障碍物。
>
> 从左上角到右下角一共有 2 条不同的路径：
>
> 1. 向右 -> 向右 -> 向下 -> 向下
> 2. 向下 -> 向下 -> 向右 -> 向右
>

**解题思路：**与上题类似，因为多了障碍物，所以需要对障碍物进行标记。初始条件中`1`代表障碍物，在后续处理中，`obstacleGrid[i][j]`代表从起点到`[i][j]`处可能的路径条数，如果存在障碍物，直接将`obstacleGrid[i][j]`赋值为0即可。**注意初始值与后续赋值的含义不同。**

- 机器人只可以向下和向右移动，因此第一行的格子只能从左边的格子移动到，第一列的格子只能从上方的格子移动到。对于剩下的格子，可以从左边或者上方的格子移动到。
- 如果格子上有障碍，那么不考虑包含这个格子的任何路径。我们从左至右、从上至下的遍历整个数组，在到达某个顶点前已经获得了到达前驱节点的方案数，这就变成了一个`动态规划`问题。我们只需要一个`obstacleGrid`数组作为 DP 数组。


> 注意： 根据题目描述，包含障碍物的格点有权值 1，我们依此来判断是否包含在路径中，然后我们可以用这个空间来存储到达这个格点的方案数。
>

1. 如果第一个格点 `obstacleGrid[0,0]` 是 `1`，说明有障碍物，机器人不能做任何移动，返回结果 `0`。
2. 如果 `obstacleGrid[0,0]` 是 `0`，初始化这个值为 `1` 然后继续算法。(此时1不代表障碍物，而是指到达该节点的方法有1种)
3. 遍历第一行，如果有一个格点初始值为 `1` ，说明当前节点有障碍物，没有路径可以通过，设值为 `0` ，而且第一行该节点的后续节点均无法到达，均赋值为`0`；否则设这个值是前一个节点的值 `obstacleGrid[i,j] = obstacleGrid[i,j-1]`。
4. 遍历第一列，如果有一个格点初始值为 `1` ，说明当前节点有障碍物，没有路径可以通过，设值为 `0` ，而且第一列该节点的后续节点均无法到达，均赋值为`0`；否则设这个值是前一个节点的值 `obstacleGrid[i,j] = obstacleGrid[i-1,j]`。
5. 从 `obstacleGrid[1,1]` 开始遍历整个数组，如果某个格点初始不包含任何障碍物，就把值赋为上方和左侧两个格点方案数之和 `obstacleGrid[i,j] = obstacleGrid[i-1,j] + obstacleGrid[i,j-1]`。如果这个点有障碍物，设值为 `0` ，这可以保证不会对后面的路径产生贡献。

**代码实现：**

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int r = obstacleGrid.length;
        int c = obstacleGrid[0].length;
        if(obstacleGrid[0][0] == 1) return 0;
        obstacleGrid[0][0] = 1;
        for(int i = 1; i < r; i++){
            //注意此处0和1的含义，0表示该节点无障碍物，1表示前继节点可到达，必须同时满足这两个条件才能到达
            obstacleGrid[i][0] = (obstacleGrid[i][0] == 0 && obstacleGrid[i - 1][0] == 1) ? 1 : 0; 
        }
        for(int i = 1; i < c; i++){
            obstacleGrid[0][i] = (obstacleGrid[0][i] == 0 && obstacleGrid[0][i - 1] == 1) ? 1 : 0; 
        }
        for(int i = 1; i < r; i++){
            for(int j = 1; j < c; j++){
                if(obstacleGrid[i][j] == 0){
                    obstacleGrid[i][j] = obstacleGrid[i - 1][j] + obstacleGrid[i][j - 1];
                }else{
                    obstacleGrid[i][j] = 0;
                }
            }
        }
        return obstacleGrid[r-1][c-1];
    }
}
```



### 4.[最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

给定一个包含非负整数的 *m* x *n* 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。

**示例:**

> 输入:
>
> [
>
>   [1,3,1],
>
>   [1,5,1],
>
>   [4,2,1]
>
> ]
>
> 输出: 7
>
> 解释: 因为路径 1→3→1→1→1 的总和最小。

**解题思路：二维动态规划**

新建一个额外的 `dp` 数组，与原矩阵大小相同。在这个矩阵中，`dp(i, j)`表示从坐标 `(i, j)`到右下角的最小路径权值。

初始化左上角角的 `dp`值为对应的原矩阵值，然后去填充整个`dp`矩阵，对于每个元素考虑移动到右边或者下面，因此获得最小路径和我们有如下递推公式：
`dp(i, j) = grid(i, j) + min(dp(i - 1, j),dp(i, j - 1))`

**代码实现：** 

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int r = grid.length;
        int c = grid[0].length;
        int[][] dp = new int[r][c];
        dp[0][0] = grid[0][0];
        for(int i = 1; i < r; i++){
            dp[i][0] = dp[i - 1][0] + grid[i][0]; 
        }
        for(int i = 1; i < c; i++){
            dp[0][i] = dp[0][i - 1] + grid[0][i]; 
        }
        for(int i = 1; i < r; i++){
            for(int j = 1; j < c; j++){
                dp[i][j] = grid[i][j] + Math.min(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[r - 1][c - 1];
    }
}
```



### 5.[解码方法](https://leetcode-cn.com/problems/decode-ways/)

一条包含字母 A-Z 的消息通过以下方式进行了编码：

'A' -> 1

'B' -> 2

...

'Z' -> 26

给定一个只包含数字的非空字符串，请计算解码方法的总数。

**示例 1:**

> 输入: "12"
>
> 输出: 2
>
> 解释: 它可以解码为 "AB"（1 2）或者 "L"（12）。

**解题思路：动态规划**

> 在不考虑边界条件的情况下，`s[i]`对应的解码方法`dp[i] = dp[i - 1] + dp[i - 2]`
>
> **原因**：（不考虑边界条件），假设给定字符串为`“111”`，很明显`dp[0] = 1`，`dp[1] = 2`
>
> ​              对s[3]来说，可能的解码方式为`“1”+11`和`“11”+1`，前者对应`dp[0]`，后者对应`dp[1]`。
>

**讨论边界条件：**

- 首先，如果第一个数字为0，则无法解码，直接返回0。
- 如果`s[i] = 0`，需要判断`s[i - 1]`是否为1或2，因为0不能单独解码，必须依附前一个数，状态转移方程为`dp[i-2]`(即如果是`110`，只能为`“1”+10`组合)，而且可能情况只有10或20。（00，30，40等情况返回0）
- 如果`s[i] != 0`,可以再分为下列情况：
  - 如果`s[i - 1] = 1`或者`s[i - 1] = 2 且 s[i] <= 6`，这种情况满足所有可能的组合，因此状态转移方程为`dp[i] = dp[i - 1] + dp[i - 2]`。
  - 如果不满足上述条件（eg：`s[i - 1] = 5， s[i] = 2`，`s[i - 1] = 2， s[i] = 8`），这种情况只能对`s[i]`单独解码（即如果是`152`，只能为`“15”+2`组合），因此状态转移方程为`dp[i-1]`。

**代码实现：**

```java
class Solution {
    public int numDecodings(String s) {
        if(s.charAt(0) == '0') return 0;
        //为了防止数组越界，将dp长度+1。因为不存在dp[-1]，所以dp[i+1]对应s[i]编码方法数。
        int[] dp = new int[s.length() + 1];
        dp[0] = 1;
        dp[1] = 1;
        for(int i = 1; i < s.length(); i++){
            if(s.charAt(i) == '0'){
                if(s.charAt(i - 1)  == '1' || s.charAt(i - 1) == '2'){
                    dp[i + 1] = dp[i - 1];
                }else{
                    return 0;
                }
            }else if(s.charAt(i - 1) == '1' || (s.charAt(i - 1) == '2' && s.charAt(i) <= '6')){
                dp[i + 1] = dp[i] + dp[i - 1];
            }else{
                dp[i + 1] = dp[i];
            }
        }
        return dp[s.length()];
    }
}
```
> 注：代码里的`dp[0]`，实际上表示的是公式里的`dp[-1]`。`-1`没有含义，但是注意到情况二和情况三，在一开始`i=1`，没有`dp[i-2]`这个值，而此时又必须给现有的编码数量加一（因为在这两种情况下多了一种编码方案），所以最好的办法就是初始化一个`dp[-1]`等于`1`。这种做法保证了代码的统一和简洁。




### 6.[最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

给定一个无序的整数数组，找到其中最长上升子序列的长度。

**示例:**

> 输入: [10,9,2,5,3,7,101,18]
>
> 输出: 4 
>
> 解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
>

**说明:**

可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。

最长上升子序列不要求在数组中连续。

**解题思路：动态规划**

- 状态定义：
  - `dp[i]`的值代表 `nums` 前 `i` 个数字中存在的的最长子序列的长度。

- 转移方程： 设 *j*∈[0,*i*) ，考虑每轮计算新 `dp[i]` 时，遍历 [0,*i*) 列表区间，做以下判断：

1. 当 `nums[i] > nums[j]` 时： `nums[i]`可以接在 `nums[j]` 之后，此时最长上升子序列长度为 `dp[j] + 1`；

2. 当 `nums[i] <= nums[j]`时： `nums[i]`无法接在 `nums[j]`之后，此情况上升子序列不成立，跳过。

	- 上述所有 1. 情况下计算出的 `dp[j] + 1`的最大值，为前 `i`个数字中存在的最长上升子序列长度（即 `dp[i]` ）。实现方式为遍历 `j` 时，每轮执行 `dp[i] = max(dp[i], dp[j] + 1)`。
	- **转移方程：** `dp[i] = max(dp[i], dp[j] + 1) for j in [0, i)`。

- 初始状态：
  - `dp[i]`所有元素值为 `1`，含义是每个元素都至少可以单独成为子序列。

- 返回值：
  - 返回 `dp` 数组最大值，即可得到全局最长上升子序列长度。

**复杂度分析：**

时间复杂度 O(N^2)： 遍历计算 `dp` 列表需 O(N)，计算每个 `dp[i]` 需 O(N)。
空间复杂度 O(N)： `dp` 列表占用线性大小额外空间。

**代码实现：**

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        if(nums.length == 0) return 0;
        int[] dp = new int[nums.length];
        int res = 0;
        Arrays.fill(dp, 1);
        for(int i = 0; i < nums.length; i++){
            for(int j = 0; j < i; j++){
                if(nums[i] > nums[j]) dp[i] = Math.max(dp[i], dp[j] + 1);
            }
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```



### 7.[三角形最小路径和](https://leetcode-cn.com/problems/triangle/)（至底向上思想）

给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

> 例如，给定三角形：
>
> [
>
> ​     [2],
>
> ​    [3,4],
>
>       [6,5,7],
>
>      [4,1,8,3]
>
> ]
>
> 自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

**解题思路：动态规划+至底向上**

> 三角形的层级`level = triangle.size() - 1`，三角形的第`i`级有`i + 1`个数字。
>

- 状态定义：`dp[i]`的值代表至底向上移动时，由下一层移动到上一层的第`i`个位置处，累计最短移动路径（向上移动一层的所有可能路径长度）

- 转移方程 ：`dp[i] = Math.min(dp[i], dp[i+1]) + triangle.get(level).get(i)`

  转移方程表示，移动到上一层的第`i`个位置处，最短的移动路径。

- 初始状态：由底层出发，因此初始值即底层权值。

- 返回值：顶层只有一个点，返回dp[0]。

*特别地：因为只需返回最小路径和，因此无需使用二维数组记录每层各点的最小路径，在上移一层后，for循环会覆盖掉下面一层对应位置的最小路径和。*


**代码实现：**

```java
public int minimumTotal(List<List<Integer>> triangle) {
    int row = triangle.size();
    int[] dp = new int[row+1];
    for (int level = row-1;level>=0;level--){
        for (int i = 0;i<=level;i++){   //第i行有i+1个数字
            dp[i] = Math.min(dp[i], dp[i+1]) + triangle.get(level).get(i);
        }
    }
    return dp[0];
}
```



### 8.[乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字）。

**示例 1:**

> 输入: [2,3,-2,4]
>
> 输出: 6
>
> 解释: 子数组 [2,3] 有最大乘积 6。

**示例 2:**

> 输入: [-2,0,-1]
>
> 输出: 0
>
> 解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。

**解题思路：**

- 遍历数组时计算当前最大值，不断更新。令max初值为-

- 令`imax`为当前最大值，则当前最大值为 `imax = max(imax * nums[i], nums[i])`。因为需要找子数组，所以相乘的数字不能间断，使用**动态规划**的思想，每次循环时`imax`值为前`i - 1`个数字中子数组乘积的最大值。

- 由于存在负数，那么会导致最大的变最小的，最小的变最大的。因此还需要维护当前最小值`imin`，`imin = min(imin * nums[i], nums[i])`，思想同上。

- 当负数出现时则`imax`与`imin`进行交换再进行下一步计算。

  eg：imax = 8， imin = 2，下个值为-3，则imax = imin * -3，imin = imax * -3

- 时间复杂度：O(n)

**代码实现：**
```java
class Solution {
    public int maxProduct(int[] nums) {
        int max = Integer.MIN_VALUE, imax = 1, imin = 1;
        for(int i = 0; i < nums.length; i++){
            if(nums[i] < 0){
                int temp = imax;
                imax = imin;
                imin = temp;
            }
            imax = Math.max(imax * nums[i], nums[i]);
            imin = Math.min(imin * nums[i], nums[i]);
            max = Math.max(max, imax);
        }
        return max;
    }
}
```

### 9.[ 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

给定正整数 *n*，找到若干个完全平方数（比如 `1, 4, 9, 16, ...`）使得它们的和等于 *n*。你需要让组成和的完全平方数的个数最少。

**示例 1:**

> 输入: n = 13
> 输出: 2
> 解释: 13 = 4 + 9.

**解题思路：** **动态规划**

- 首先初始化长度为`n+1`的数组`dp`，每个位置都为`0`

- `dp[i]`记录的值为和为`i`的平方数最小个数，如果`n`为`0`，则`dp[0]=0`

- 对数组进行遍历，下标为`i`，每次都先将当前数字更新为最大的结果，即`dp[i]=i`，比如`i=4`，最坏结果为`4=1+1+1+1`即为`4`个数字

- 动态转移方程为：`dp[i] = MIN(dp[i], dp[i - j * j] + 1)`，`i`为下标，`j*j`表示平方数。

  **解释:**

  > 给定正整数 `n`，和为`n`的完全平方数最小个数`m`满足：`m = dp[n]` 
  >
  > 令 `j*j` 为满足最小完全平方数个数 `m` 的时候，最大的平方数  。 令  `d + j * j = n ;  d >= 0;` 
  >
  > 注意：一定要是满足`m`最小的时候的`j`值,一味的取最大平方数,就是贪心算法了
  >
  > 得出 `dp[d] + dp[j*j] = dp[n]`，即：`和为d的完全平方数个数 + 和为j*j的完全平方数个数 = m = dp[n]`
  >
  > 和为`j*j`的最小完全平方数个数显然为 `dp[j*j] = 1`；则 `dp[d] + 1 = dp[n]`; 
  >
  > 因为 `d = n - j*j`；则可以推出`dp[n - j*j] + 1 = dp[n]` ;  且 `j * j <= n`;


- 时间复杂度：`O(n*sqrt(n))`，sqrt为平方根

**代码实现：**
```java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1]; // 默认初始化值都为0
        for (int i = 1; i <= n; i++) {
            dp[i] = i; // 最坏的情况就是每次+1
            for (int j = 1; i - j * j >= 0; j++) { 
                dp[i] = Math.min(dp[i], dp[i - j * j] + 1); // 动态转移方程
            }
        }
        return dp[n];
    }
}
```



### 10.[二维区域和检索 - 矩阵不可变](https://leetcode-cn.com/problems/range-sum-query-2d-immutable/)

给定一个二维矩阵，计算其子矩形范围内元素的总和，该子矩阵的左上角为 (*row*1, *col*1) ，右下角为 (*row*2, *col*2)。

**示例:**

> 给定 matrix = [
>
>   [3, 0, 1, 4, 2],
>
>   [5, 6, 3, 2, 1],
>
>   [1, 2, 0, 1, 5],
>
>   [4, 1, 0, 1, 7],
>
>   [1, 0, 3, 0, 5]
>
> ]
>
> sumRegion(2, 1, 4, 3) -> 8
>
> sumRegion(1, 1, 2, 2) -> 11
>
> sumRegion(1, 2, 2, 4) -> 12

**说明:**

1. 你可以假设矩阵不可变。
2. 会多次调用 sumRegion 方法。
3. 你可以假设 row1 ≤ row2 且 col1 ≤ col2。

**解题思路：动态规划**

- 二维数组`dp[i][j]`代表从矩阵`（0,0）`至`（i,j）`处的元素和。

  > 注意到：`dp[i][j]=matrix[i][j]+dp[i][j-1]+dp[i-1][j]-dp[i-1][j-1]`
  >
  > 为防止在`i,j=0`时，上式出现数组越界，将二维数组`dp`的行列长度`+1`，即`dp[i+1][j+1]`对应原始矩阵`（0,0）`至`（i,j）`处的元素和。

- **状态转移方程**：`dp[i+1][j+1]=matrix[i][j]+dp[i+1][j]+dp[i][j+1]-dp[i][j]`

- **范围（r1,c1,r2,c2）的元素和等于**：`dp[r2+1][c2+1]-dp[r2+1][c1]-dp[r1][c2+1]+dp[r1,c1]`

- 时间复杂度：每次查询时间 `O(1)`，`O(mn)`的时间预计算。构造函数中的预计算需要 `O(mn)` 时间。每个 sumregion 查询需要 `O(1)` 时间 。

- 空间复杂度：`O(mn)`，该算法使用 `O(mn)` 空间存储累积区域和。

**代码实现：**
```java
class NumMatrix {
    private int[][] dp;

    public NumMatrix(int[][] matrix) {
        if (matrix.length == 0 || matrix[0].length == 0) return;
        dp = new int[matrix.length + 1][matrix[0].length + 1];
        for (int r = 0; r < matrix.length; r++) {
            for (int c = 0; c < matrix[0].length; c++) {
                dp[r + 1][c + 1] = matrix[r][c] + dp[r + 1][c] + dp[r][c + 1] - dp[r][c];
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return dp[row2 + 1][col2 + 1] - dp[row1][col2 + 1] - dp[row2 + 1][col1] + dp[row1][col1];
    }
}
```

### 11.[编辑距离](https://leetcode-cn.com/problems/edit-distance/)

给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

- 插入一个字符
- 删除一个字符
- 替换一个字符

**示例：**

> 输入：word1 = "horse", word2 = "ros"
>
> 输出：3
>
> 解释：
>
> horse -> rorse (将 'h' 替换为 'r')
>
> rorse -> rose (删除 'r')
>
> rose -> ros (删除 'e')

**解题思路：动态规划**

word1和word2的编辑距离为X，意味着word1经过X步，变成了word2，并且X是个最少的步数。

我们有word1和word2，定义`dp[i][j]`的含义为：word1的前`i`个字符和word2的前`j`个字符的编辑距离。意思就是word1的前`i`个字符，变成word2的前`j`个字符，最少需要这么多步。

例如`word1 = "horse", word2 = "ros"`，那么`dp[3][2]=X`就表示`"hor"`和`“ro”`的编辑距离，即把`"hor"`变成`“ro”`最少需要X步。

如果下标为零则表示空串，比如：`dp[0][2]`就表示空串`""`和`“ro”`的编辑距离

**定理一**：如果其中一个字符串是空串，那么编辑距离是另一个字符串的长度。

> 比如空串“”和“ro”的编辑距离是2（做两次“插入”操作）。再比如"hor"和空串“”的编辑距离是3（做三次“删除”操作）。

**定理二**：当`i>0,j>0`时（即两个串都不空时）：

> - 若 word1 和 word2 的最后一个字母相同：
>
> `dp[i][j]=min(dp[i][j−1]+1,dp[i−1][j]+1,dp[i−1][j−1])`
>
> - 若 word1 和 word2 的最后一个字母不同：
>
> `dp[i][j]=1+min(dp[i][j−1],dp[i−1][j],dp[i−1][j−1])`

举个例子，`word1 = "abcde", word2 = "fgh"`,我们现在算这俩字符串的编辑距离，就有三种方式：

1. 已知`"abcd"`变成`"fgh"`需要的步数（假设X步），那么从`"abcde"`到`"fgh"`就是`"abcde"->"abcd"->"fgh"`。（一次删除，总共X+1步）
2. 已知`"abcde"`变成`“fg”`需要的步数（假设Y步），那么从`"abcde"`到`"fgh"`就是`"abcde"->"fg"->"fgh"`。（先Y步，再一次添加，总共Y+1步）
3. 已知`"abcd"`变成`“fg”`需要的步数（假设Z步），那么从`"abcde"`到`"fgh"`就是`"abcde"->"fge"->"fgh"`。（先不管最后一个字符，把前面的先变好，用了Z步，然后把最后一个字符给替换了。需要判断最后一个字符是否相同，如果最后一个字符碰巧就一样，那就不用替换，省了一步）

以上三种方式算出来选最少的，就是答案。

**代码实现：**
```java
class Solution {
  public int minDistance(String word1, String word2) {
    int n = word1.length();
    int m = word2.length();
    // 如果有一个字符串为空串，则返回非空串的长度（对应全部插入或删除的操作）
    if (n * m == 0)
      return n + m;
    // 定义DP数组
    int [][] dp = new int[n + 1][m + 1];
    // 边界状态初始化
    for (int i = 0; i < n + 1; i++) {
      dp[i][0] = i;
    }
    for (int j = 0; j < m + 1; j++) {
      dp[0][j] = j;
    }
    // 计算所有 DP 值
    for (int i = 1; i < n + 1; i++) {
      for (int j = 1; j < m + 1; j++) {
        int delet = dp[i - 1][j] + 1;
        int add = dp[i][j - 1] + 1;
        int replace = dp[i - 1][j - 1];
        if (word1.charAt(i - 1) != word2.charAt(j - 1))
          replace += 1;
        dp[i][j] = Math.min(delet, Math.min(add, replace));
      }
    }
    return dp[n][m];
  }
}
```