---
layout: post
title: 递归、回溯套路
date: 2020-02-20
author: WangY.
tags: [Java, code, LeetCode, 递归, 回溯]
comments: true
toc: true
---

目前正在准备实习面试，在LeetCode上刷相关题目。

常有典型的递归回溯题，本文详细介绍递归回溯的套路。

[原文作者：reedfan](https://leetcode-cn.com/problems/subsets/solution/xiang-xi-jie-shao-di-gui-hui-su-de-tao-lu-by-reedf/)



## 1 从[39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)说起

### 1.1 题目介绍

给定一个无重复元素的数组 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的数字可以无限制重复被选取。

说明：

- 所有数字（包括 target）都是正整数。

- 解集不能包含重复的组合。 

> 示例 1:
>
> 输入: candidates = [2,3,6,7], target = 7,
>
> 所求解集为:
>
> [
>
>   [7],
>
>   [2,2,3]
>
> ]
>
> 示例 2:
>
> 输入: candidates = [2,3,5], target = 8,
>
> 所求解集为:
>
> [
>
>   [2,2,2,2],
>
>   [2,3,3],
>
>   [3,5]
>
> ]

### 1.2 解题思路

题目给出的算法结构为：

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {

    }
}
```

首先题目要求返回的类型为 `List<List<Integer>>`，那么我们就新建一个 `List<List<Integer>>` 作为全局变量，最后将其返回。

```java
class Solution {
    List<List<Integer>> lists = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
       
        return lists;
    }
}
```

再看看返回的结构，`List<List<Integer>>`。因此我们需要写一个包含 `List<Integer>` 的辅助函数，加上一些判断条件，此时结构变成了

```java
class Solution {
    List<List<Integer>> lists = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        if (candidates == null || candidates.length == 0 || target < 0) {
            return lists;
        }

        List<Integer> list = new ArrayList<>();
        process(candidates, target, list);
        return lists;
    }
    
    private void process(int[] candidates, int target, List<Integer> list) {

    }
}
```

重点就是如何进行递归。递归的第一步，当然是写递归的终止条件啦，没有终止条件的递归会进入死循环。那么有 哪些终止条件呢？由于条件中说了都是正整数。因此，如果 `target<0`,当然是要终止了，如果 `target==0`，说明此时找到了一组数的和为 `target`，将其加进去。此时代码结构变成了这样。

```java
class Solution {
    List<List<Integer>> lists = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        if (candidates == null || candidates.length == 0 || target < 0) {
            return lists;
        }

        List<Integer> list = new ArrayList<>();
        process(candidates, target, list);
        return lists;
    }
    
    private void process(int[] candidates, int target, List<Integer> list) {
        if (target < 0) {
            return;
        }
        if (target == 0) {
            lists.add(new ArrayList<>(list));
        }

    }
}
```

我们是要求组成 `target` 的组合。因此需要一个循环来进行遍历。每遍历一次，将此数加入 `list`，然后进行下一轮递归。代码结构如下。

```java
class Solution {
    List<List<Integer>> lists = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        if (candidates == null || candidates.length == 0 || target < 0) {
            return lists;
        }

        List<Integer> list = new ArrayList<>();
        process(candidates, target, list);
        return lists;
    }
    
    private void process(int[] candidates, int target, List<Integer> list) {
        if (target < 0) {
            return;
        }
        if (target == 0) {
            lists.add(new ArrayList<>(list));
        } else {
            for (int i = 0; i < candidates.length; i++) {
                list.add(candidates[i]);
                //因为每个数字都可以使用无数次，所以递归还可以从当前元素开始
                process( candidates, target - candidates[i], list);
      
            }
        }
    
    }
}
```

进行测试后会发现一个规律，后面的一个组合会包含前面一个组合的所有的数字，而且这些数加起来和 target 也不相等啊。原因出在哪呢？java 中除了几个基本类型，其他的类型可以算作引用传递。这就是导致 list 数字一直变多的原因。因此，在每次递归完成，我们要进行一次回溯。把最新加的那个数删除。此时代码结构变成这样。

```java
class Solution {
    List<List<Integer>> lists = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        if (candidates == null || candidates.length == 0 || target < 0) {
            return lists;
        }

        List<Integer> list = new ArrayList<>();
        process(candidates, target, list);
        return lists;
    }
    
    private void process(int[] candidates, int target, List<Integer> list) {
        if (target < 0) {
            return;
        }
        if (target == 0) {
            lists.add(new ArrayList<>(list));
        } else {
            for (int i = 0; i < candidates.length; i++) {
                list.add(candidates[i]);
                //因为每个数字都可以使用无数次，所以递归还可以从当前元素开始
                process( candidates, target - candidates[i], list);
                list.remove(list.size() - 1);
            }
        }
    
    }
}
```

测试后发现这次虽然加起来都等于 7 了，但包含了重复的组合。为什么会有重复的组合呢？因为每次递归我们都是从 0 开始，所有数字都遍历一遍。所以会出现重复的组合。改进一下，只需加一个 start 变量即可。

```java
List<List<Integer>> lists = new ArrayList<>();

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        if (candidates == null || candidates.length == 0 || target < 0) {
            return lists;
        }
    
        List<Integer> list = new ArrayList<>();
        process(0, candidates, target, list);
        return lists;
    }
    
    private void process(int start, int[] candidates, int target, List<Integer> list) {
        //递归的终止条件
        if (target < 0) {
            return;
        }
        if (target == 0) {
            lists.add(new ArrayList<>(list));
        } else {
            for (int i = start; i < candidates.length; i++) {
                list.add(candidates[i]);
                //因为每个数字都可以使用无数次，所以递归还可以从当前元素开始
                process(i, candidates, target - candidates[i], list);
                list.remove(list.size() - 1);
            }
        }
    }
```

## 2 再看一题[77. 组合](https://leetcode-cn.com/problems/combinations/)

### 2.1 题目介绍

给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

> 示例:
>
> 输入: n = 4, k = 2
>
> 输出:
>
> [
>
>   [2,4],
>
>   [3,4],
>
>   [2,3],
>
>   [1,2],
>
>   [1,3],
>
>   [1,4],
>
> ]



### 2.2 解题思路

题目给出的算法结构为

```java
class Solution {
    public List<List<Integer>> combine(int n, int k) {
    }
}
```

按照前面的套路，首先建一个 `ArrayList<List<Integer>> res` 作为全局变量。顺带建一个含有 `List<Integer>list` 的辅助函数。

此时结构变成如下所示。

```java
class Solution {
     private ArrayList<List<Integer>> res;
    // 求解C(n,k), 当前已经找到的组合存储在c中, 需要从start开始搜索新的元素
    private void generateCombinations(int n, int k, int start, List<Integer> list){
        
    }
    
    public List<List<Integer>> combine(int n, int k) {
    
        res = new ArrayList<>();
        if(n<=0 || k<=0 || k>n){
            return res;
        }
        List<Integer> list = new ArrayList<>();
        generateCombinations(n,k,1,list);       
        return res;
    }
}
```

对于辅助递归函数。第一步当然是列出终止条件，避免进入死循环。由于题目要求是所有 k 个数的组合。那么很容易知道递归的终止条件为 `list.size() == k`。
因此，代码结构可变为如下所示。

```java
class Solution {
     private ArrayList<List<Integer>> res;
    // 求解C(n,k), 当前已经找到的组合存储在c中, 需要从start开始搜索新的元素
    private void generateCombinations(int n, int k, int start, List<Integer> list){
        if(list.size() == k){
            res.add(new ArrayList<>(list));
            return;
        }
    }

    public List<List<Integer>> combine(int n, int k) {
    
        res = new ArrayList<>();
        if(n<=0 || k<=0 || k>n){
            return res;
        }
        List<Integer> list = new ArrayList<>();
        generateCombinations(n,k,1,list);
        return res;    
    }
}
```

接下来要进入重头戏了，递归回溯的整个过程。整个递归过程不就是一直将数字加入 `list` 么。因此可以用一个循环，在循环中主要做三件事：

1. 加此轮的数据加入 list。
2. 递归进行下一步调用。
3. 删除此轮加入的数据进行回溯。

此时代码结构如下：

```java
class Solution {
     private ArrayList<List<Integer>> res;
    // 求解C(n,k), 当前已经找到的组合存储在c中, 需要从start开始搜索新的元素
    private void generateCombinations(int n, int k, int start, List<Integer> list){
        if(list.size() == k){
            res.add(new ArrayList<>(list));
            return;
        }
        for (int i = start; i <= n ; i++) {
            list.add(i);
            generateCombinations(n,k,i+1,list);
            list.remove(list.size()-1);

        }
    }
    
    public List<List<Integer>> combine(int n, int k) {
    
        res = new ArrayList<>();
        if(n<=0 || k<=0 || k>n){
            return res;
        }
        List<Integer> list = new ArrayList<>();
        generateCombinations(n,k,1,list);
        return res;
    }
}
```

进行测试，通过是通过了，但是真的慢成狗啊。

分析一下原因所在。假设 `n = 100`, `k= 90`, `i = 15`，`list` 里面就 3 个数据。

按照上面的代码，我们还需要继续循环 85 次。但是想一下，即使后面的循环每次都加一个数据，最后也才 88 个数据。

不能形成一组解。也就是说这些循环都是在做无用功，因此引出一个很重要的知识点 **剪枝**

循环的终止条件不应该是 `i<=n`, 应该是 `i-1 +(k-list.size()) <= n`。

因此代码如下:

```java
class Solution {
     private ArrayList<List<Integer>> res;
    // 求解C(n,k), 当前已经找到的组合存储在c中, 需要从start开始搜索新的元素
    private void generateCombinations(int n, int k, int start, List<Integer> list){
        if(list.size() == k){
            res.add(new ArrayList<>(list));
            return;
        }
        for (int i = start; i <= n-(k-list.size())+1 ; i++) {
            list.add(i);
            generateCombinations(n,k,i+1,list);
            list.remove(list.size()-1);
        }
    }
    
    public List<List<Integer>> combine(int n, int k) {
        res = new ArrayList<>();
        if(n<=0 || k<=0 || k>n){
            return res;
        }
        List<Integer> list = new ArrayList<>();
        generateCombinations(n,k,1,list);
        return res;
    }
}
```



## 3 面试题[78. 子集](https://leetcode-cn.com/problems/subsets/)

### 3.1 题目介绍

给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

> 示例:
>
> 输入: nums = [1,2,3]
>
> 输出:
>
> [
>
>   [3],
>
>   [1],
>
>   [2],
>
>   [1,2,3],
>
>   [1,3],
>
>   [2,3],
>
>   [1,2],
>
>   []
>
> ]



### 3.2 总结回溯法

[回溯法](https://baike.baidu.com/item/回溯法/86074?fr=aladdin)是一种探索所有潜在可能性找到解决方案的算法。如果当前方案不是正确的解决方案，或者不是最后一个正确的解决方案，则回溯法通过修改上一步的值继续寻找解决方案。

![img](https://pic.leetcode-cn.com/Figures/78/backtracking.png)

  


### 3.3 解题思路

**算法**

定义一个回溯方法 `backtrack(first, curr)`，第一个参数为索引 `first`，第二个参数为当前子集 `curr`。

- 如果当前子集构造完成，将它添加到输出集合中。

- 否则，从 `first` 到 `n` 遍历索引 `i`。

  将整数 `nums[i]` 添加到当前子集 `curr`。

  继续向子集中添加整数：`backtrack(i + 1, curr)`。

  从 `curr` 中删除 `nums[i]` 进行回溯。
  

本题使用递归回溯解法的代码为：

```java
class Solution {
  List<List<Integer>> output = new ArrayList();
  //n为数组长度，k用来判断是否进行了所有可能的组合
  int n, k;

  //回溯法
  public void backtrack(int first, ArrayList<Integer> curr, int[] nums) {
    //当前子集构造完成
    if (curr.size() == k)
      //添加到输出集合中
      output.add(new ArrayList(curr));
	
    //从first到n遍历索引i
    for (int i = first; i < n; ++i) {
      //将整数num[i]添加到当前子集
      curr.add(nums[i]);
      //使用下一个数来填充子集
      backtrack(i + 1, curr, nums);
      //回溯
      curr.remove(curr.size() - 1);
    }
  }

  public List<List<Integer>> subsets(int[] nums) {
    n = nums.length;
    //通过for循环改变k值，遍历所有可能的组合
    //eg：k=0:[];k=1:[1],[2],[3];k=2:[1,2],[1,3],[2,3];k=3:[1,2,3]
    for (k = 0; k < n + 1; ++k) {
      backtrack(0, new ArrayList<Integer>(), nums);
    }
    return output;
  }
}
```