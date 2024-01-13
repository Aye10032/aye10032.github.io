---

layout: post

title: "算法设计与分析作业"

date: 2024-01-13
updated: 2024-01-13

excerpt: "国科大秋季学期卜东波算法设计与分析作业"

categories: 
	- 学习

tag: 
	- 学习

math: true

category_bar: true
typora-root-url: ..
---



# 一、分治

{% note info%}
**主定理**

$$
T(n)=aT\left(\dfrac nb\right) + f(n^d)=\begin{cases}
O(n^{\log_ba}) & d<\log_ba\\
O(n^{\log_ba}\log n) &d=\log_ba\\
O(n^d)&d>\log_ba
\end{cases}
$$

其中：

- a：子问题的数量
- b：子问题规模与原问题的比
- $f(n^d)$：合并子问题以及除了递归调用之外的算法的时间复杂度

{% endnote %}



## Q1

### 题目

You are interested in analyzing some hard-to-obtain data from two separate databases. Each database contains n numerical values, so there are 2n values total and you may assume that no two values are the same. You’d like to determine the median of this set of 2n values, which we will define here to be the nth smallest value. However, the only way you can access these values is through queries to the databases. In a single query, you can specify a value k to one of the two databases, and the chosen database will return the kth smallest value that it contains. Since queries are expensive, you would like to compute the median using as few queries as possible. Give an algorithm that finds the median value using at most $O(\log n)$ queries.



### 思路

1. 首先对两个数据库各取中位数$\frac{n}{2}$
2. 比较两中位数的大小，假设其中较小的一个为M1，较大的为M2
3. 全局中位数必然位于相应数据库中大于M1或小于M2的部分中
4. 取这两部分作为新的数据库，再次重复上述操作
5. 直到数据库长度为1时停止，由于本题令中位数为全局的第N个，因此这里取两个中较小的一个即为答案



### 伪代码

```python
def FINDMID(len, k1, k2):
	M1 = ask(1, k1)
	M2 = ask(2, k2)
	
    if len == 1:
        return min(M1, M2)
    else:
		if M1<M2:
            FINDMID(len/2, k1 + k1/2, k2 - k2/2)
        else:
            FINDMID(len/2, k1 - k1/2, k2 + k2/2)
```



### 时间复杂度

由于需要查询直到数据库长度为1，表现为树就是需要一直遍历到树底，则迭代次数为$\log(n)$，每次需要进行两次查询操作，时间复杂度均为1，故总时间复杂度为
$$
T(n) = T(\frac{n}{2}) + c = O(\log n)
$$



## Q2

### 题目

Given any 10 points, $p_1, p_2, \dots, p_{10}$, on a two-dimensional Euclidean plane, please write an algorithm to find the distance between the closest pair of points. 

1. Using a brute-force algorithm to solve this problem, analyze the time complexity of your implemented brute-force algorithm and explain why the algorithm’s time complexity is $O(n^2)$, where n is the number of points.
2. Propose an improved algorithm to solve this problem with a time complexity better than the brute-force algorithm. Describe the algorithm’s idea and analyze its time complexity.



### 1、暴力解

#### 思路

1. 遍历每一个点，计算它与其他点的距离
2. 遍历过程中保存当前MAX值，并不断更新
3. 返回结果



#### 伪代码

```python
def SOLVE():
    max_value = 0
    for i in range 10:
        for j in range 10:
            max_value = max(max_value, GET_DISTANCE(p[i], p[j]))
    return max_value
```



### 2、分治优化

#### 思路

1. 首先将所有点按照X轴坐标排序
2. 对于排序好的点，平分成两个子集
   - 若该子集中只有不超过三个点，则使用暴力法算出最近距离
   - 否则继续分成两个子集
3. 经过上述操作分别得到左右点集中的最短点对距离，并取其中的最小值记为w
4. 对于点集分界线两侧w宽度区域内的点，按照y轴进行排序
5. 遍历排序后的点，分别计算它与其他点的距离，并与w比较，取最小值
6. 返回w



#### 伪代码

```python
SORT_BY_X(P)

def FIND(P, l, r):
    if P[l:r].size <= 3:
        return GET_MIN(P)
    else:
        w1 = FIND(P, l, (l+r)/2)
        w2 = FIND(P, (l+r)/2, r)
        
        w = min(w1, w2)
        
        P_new = P[(l+r)/2-w:(l+r)/2+w]
        SORT_BY_Y(P_new)
        
        for point in P_new:
            h = GET_DIS(point, P_new)
            w = min(w, h)
        
        return w
```



#### 时间复杂度：

最开始需要进行一次全体排序，时间复杂度为$O(n\log{n})$，之后FIND部分，包含两个递归调用和一次排序、一次遍历：
$$
T(n)= 2T(\frac{n}{2}) + O(n\log{n}) + O(n) = O(n\log^2{n})
$$



## Q3

### 题目

Given an integer $n$, where $100 < n < 10000$, please design an efficient algorithm to calculate $3^n$, with a time complexity not exceeding $O(n)$. 

1. Implement a naive calculation method to compute $3^n$ and analyze the time complexity of the naive calculation method. 
2. Propose an improved algorithm to calculate 3n with a time complexity not exceeding $O(n)$. Describe the algorithm’s concept and analyze its time complexity



### 1、暴力解

#### 思路

对于输入的n，直接进行一个N次循环，每次*3



#### 伪代码

```python
def CAL(n):
    sum = 1
    for i in range n:
        sum = sum * 3
    return sum
```



#### 时间复杂度

由于仅有一个循环，且循环内仅有一次操作，故总的时间复杂度为$O(n)$



### 2、优化

#### 思路

- 若n为0，直接返回1
- 若n为偶数，则$3^n=(3^{\frac{n}{2}})^2$
- 若n为奇数，则$3^n=3^{n-1}\times3$



#### 伪代码

```python
def CAL(n):
    if n == 0:
        return 1
    
    if n%2 == 0:
        return CAL(n/2)**2
    else:
        return CAL(n-1)*3
```



#### 时间复杂度

每次递归需要的运算为平方、乘法，时间复杂度均为$O(1)$，递归次数为 $\log n$，因此时间复杂度为
$$
T(n) = \begin{cases}
1 & n = 0
\\
T(\frac{n}{2}) + c = O() & n\neq 0
\end{cases}
$$





## Q4

### 题目

Given a binary tree T, please give an $O(n)$ algorithm to invert binary tree. For example below, inverting the left binary tree, we get the right binary tree.



### 思路

- 对于每一个节点的左右孩子，分两种情况：
  - 是叶子节点，下一步
  - 不是叶子节点，递归调用
- 两个子节点处理结束之后，对换左右孩子



### 伪代码

```python
def INVERT(Node N):
    if N.L是叶子节点 && N.R是叶子节点:
        swap(N.L, N.R)
        return
    else:
        if not N.L是叶子节点:
            INVERT(N.L)

        if not N.R是叶子节点:
            INVERT(N.R)

        swap(N.L, N.R)
```



### 时间复杂度

每一次递归中进行的运算为交换左右孩子，时间夫复杂度为$O(1)$，递归调用的次数等于节点数，因此时间复杂度为$O(n)$





## Q5

### 题目

There are $N$ rooms in a prison, one for each prisoner, and there are $M$ religions, and each prisoner will follow one of them. If the prisoners in the adjacent room are of the same religion, escape may occur. Please give an $O(n)$ algorithm to find out how many states escape can occur. For example, there are 3 rooms and 2 kinds of religions, then 6 different states escape will occur.



### 思路

由于直接计算会越狱的情况比较复杂，转而求出总的情况和不会越狱的情况，相减得到会越狱的数量。

对于不会越狱，实际上就是要求相邻两个牢房的宗教信仰不一样即可，设第一间房间有m种可能，则他的相邻房间不会越狱的可选情况有m-1种，再相邻的依旧。因此实际上是求
$$
m^n - m(m-1)^{n-1}
$$
而求幂函数在Q3中已经有优化方法，这里直接使用



### 伪代码

```python
def SQUARING(m, n):
    if n == 0:
        return 1
    
    if n%2 == 0:
        return SQUARING(n/2)**2
    else:
        return SQUARING(n-1)*m
    
def CAL(M, N):
    sum = SQUARING(M, N)
    success = SQUARING(M-1, N-1) * M
    
    return sum - success
    
```



### 时间复杂度

核心操作为两次幂运算，每次的时间复杂度均为$\log{n}$，故总的时间复杂度仍然为$\log{n}$

$$
T(n) = 2T(\frac{n}{2}) + O(1) = O(\log{n})
$$


# 二、动态规划

## Q1 Money Robbing

### 题目

A robber is planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night. 

1. Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police. 
2. What if all houses are arranged in a circle?

### 1、普通解

#### 思路

- **状态**：每一个房子有两种状态，抢劫或不抢，i表示第i个房子
- **状态转移方程**：

$$
dp[i] = \max
\begin{cases}
money_i+dp[i-2]\\
dp[i-1]
\end{cases}
$$

- **初始状态**：
  - dp[0]=0
  - dp[1]=1
- **计算顺序**：采用自底向上的方式，一直计算到dp[n]，其值即为所求最大值



#### 伪代码

```python
function robHouses(money):
    n = length(money)
    if n == 0:
        return 0
    if n == 1:
        return money[1]

    dp = new Array(n+1)
    dp[0] = 0
    dp[1] = money[1]

    for i = 2 to n:
        dp[i] = max(dp[i-1], dp[i-2] + money[i])

    return dp[n]
```



#### 时间复杂度

子问题的个数为n，每一个问题的求解时间为1，因此此算法的时间复杂度为O(n)



### 2、环形街道

#### 分析

相比较于原题，当街道是环形时，将整个问题分为两个小的子问题：不抢第一个房子和不抢最后一个房子，在这两个情况下分别DP求出最优解，取其中较大的一个作为答案即可。



#### 伪代码

```python
function robHouses(money): 
    n = length(money) 
    if n == 0: 
        return 0 
    if n == 1: 
        return money[0]

    // 不抢劫第一座房子的情况
    dp1[1] = money[0]

    // 不抢劫最后一座房子的情况
    dp2[0] = 0
    dp2[1] = money[1]

    for i = 1 to n-1:
        dp1[i] = max(dp1[i-1], dp1[i-2] + money[i])

    for i = 2 to n:
        dp2[i] = max(dp2[i-1], dp2[i-2] + money[i])

    return max(dp1[n-1], dp2[n])
```



#### 时间复杂度

虽然拆分成了两个子问题，但是时间复杂度仍然为O(n)



## Q2 Ugly Number

### 题目



### 1、暴力解

#### 思路

直接从1开始遍历所有自然数，并判断每一个数是否为丑数。具体方法为一直除2、3、5，并看最后余数是否为1

An ugly number is a positive integer whose prime factors are limited to 2, 3, and 5. Given an integer $n$, return the $n^{th}$ ugly number. 

1. Using a brute-force algorithm to solve this problem, analyze the time complexity of your implemented brute-force algorithm and explain why the algorithm’s time complexity is $O(n^2)$, where $n$ is the number of points. 
2. Propose an improved algorithm to solve this problem with a time complexity better than the brute-force algorithm. Describe the algorithm’s idea and analyze its time complexity.



#### 伪代码

```python
function is_ugly(num):
    while num % 2 == 0:
        num /= 2
    while num % 3 == 0:
        num /= 3
    while num % 5 == 0:
        num /= 5
    return num == 1

function nth_ugly_number(n):
    count = 0
    num = 1
    while count < n:
        if is_ugly(num):
            count += 1
        num += 1
    return num - 1
```





### 2、动态规划

#### 思路

- **状态**：对于每一个数，它必然是由$2^{p_2}\times 3^{p_3} \times 5^{p_5}$所组成的，即在之前的某个丑数的基础上乘以2、3或5得到
- **状态转移方程**

$$
dp[i] = \min
\begin{cases}
dp[p2] * 2\\
dp[p3] * 3\\
dp[p5] * 5
\end{cases}
$$

- **初始状态**：初始时p2=p3=p5=1，dp[0] = 1
- **计算顺序**：从1开始，逐个递推，每次将选中的$p_i$更新为它要指向的下一个要乘以i的丑数，dp[n]即为第n个丑数



#### 伪代码

```python
function nth_ugly_number(n):
    dp[1] = 1
    p2, p3, p5 = 1, 1, 1

    for i = 2 to n：
        next_ugly = min(dp[p2] * 2, dp[p3] * 3, dp[p5] * 5)
        dp[i] = next_ugly

        if next_ugly == dp[p2] * 2：
            p2++
        elif next_ugly == dp[p3] * 3：
            p3++
        elif next_ugly == dp[p5] * 5：
            p5++

    return dp[n]
```



#### 时间复杂度

子问题的个数为N-1，每次运算的复杂度为常数级，不存在递归调用，因此总的时间复杂度为O(n)



## Q3 Unique Binary Search Trees

### 题目

Given $n$, how many structurally unique BST’s (binary search trees) that store values 1...n? 

Note: Given $n = 3$, there are a total of 5 unique BST’s

### 思路

- **状态**：用dp[i]表示取i为二叉树的根，则可以将全部的数字分为[1:i-1]和[i+1:n]两部分，分别属于左右子树

- **状态转移方程**：每一个根确定的情况下，通过累加求得右子树乘以左子树的值

$$
dp[i] = \sum_{j=1}^i\left(dp[j-1]\times dp[i-j]\right)
$$

- **初始状态**：显然，dp[0]=dp[1]=1
- **计算顺序**：从2开始循环遍历直到n，dp[n]的值即为所求结果



### 伪代码

```python
function numTrees(n):
    dp[0] = dp[1] = 1

    for i = 2 to n:
        dp[i] = 0
        for j = 1 to i:
            dp[i] += dp[j-1] * dp[i-j]

    return dp[n]
```



### 时间复杂度

本题外层循环n-1次，对于每个内层，循环的次数为$\sum i$，因此，总的时间复杂度为$O(n^2)$



## Q4 Largest Divisible Subset

### 题目

Given a set of distinct positive integers, find the largest subset such that every pair $(S_i, S_j)$ of elements in this subset satisfies: $S_i\%S_j = 0$ or $S_j\%S_i = 0$. Please return the largest size of the subset. 

Note: $S_i\%S_j = 0$ means that $S_i$ is divisible by $S_j$.

### 思路

- **状态**：首先假设所给集合为升序排列，用dp[i]表示以第i个数结尾的最大子集大小
- **转移方程**：对于每一个数字num[i]，若它可以被某个小于它的数num[j]整除，则它也一定可以被能够整除num[j]的所有数整除，即有

$$
dp[i] = \max
\begin{cases}
dp[i]\\
dp[j] + 1
\end{cases}
$$

- **初始状态**：初始化所有dp[i]=1，因为显然数字自己成为一个子集



### 伪代码

```python
function largestSubset(nums):
	maxSize = 1

    for i from 1 to n-1:
        for j from 0 to i:
            if nums[j] % nums[i] == 0:
                dp[i] = max(dp[i], dp[j] + 1)

        maxSize = max(dp[i], maxSize)

    return maxSize
```



### 时间复杂度

本题中，外层循环的次数为n-1，内层次数为$\sum i$，因此总的时间复杂度为$O(n^2)$



## Q5 Target Sum

### 题目

You are given an integer array nums and an integer target. You want to build an expression out of nums by adding one of the symbols ’+’ and ’-’ before each integer in nums and then concatenate all the integers. 

For example, if nums = [2, 1], you can add a ’+’ before 2 and a ’-’ before 1 and concatenate them to build the expression ”+2-1”. 

Return the number of different expressions that you can build, which evaluates to target.

Example: 

- **Input**: nums = [1,1,1,1,1], target = 3 
- **Output**: 5 
- **Explanation**: There are 5 ways to assign symbols to make the sum of nums be target 3.

$$
-1 + 1 + 1 +1+1=3\\
 +1- 1 + 1 +1+1=3\\
 +1 + 1- 1 +1+1=3\\
 +1 + 1 +1-1+1=3\\
 +1 + 1 +1+1-1=3
$$



### 思路

对于每一个数字而言，只有+和-两种选择，那么假设所有加的数为一个集合S1，则剩下的数必然属于减去的集合S2。显然，有
$$
\begin{align}
target &= \sum S1_{i} - \sum S2_i
\\
&= \sum S_i - 2\sum S2_i
\end{align}
$$
即：
$$
\sum S2_i = \frac{\sum S - target}{2}
$$
则所求问题变为：从全集S中挑选n个数，组成一个集合S2，使得S2的求和等于S的总和减去target

- **状态**：取$dp[i][j]$表示对于前i个数，和为j的方案数
- **转移方程**：令S2的求和等于S的总和减去target的值为diff，则对于小于diff的每一个j，计算$dp[i][j]$。显然，要想取这个数，j必然大于等于$num[i]$，因此有

$$
dp[i]=
\begin{cases}
dp [i-1][j] & j < nums[i]\\
dp[i-1][j] + dp[i-1][j-nums[i]] & j\geq nums[i]
\end{cases}
$$



- **初始状态**：当S为空集时，和为0，方案数为1（即什么都不选），因此$dp[0][0]=1$



### 伪代码

```
function findTargetSumWays(nums[], target): 
	sum = 0 
	for num in nums: 
		sum += num 
    diff = (sum - target)/2 
    
    dp[0][0] = 1 
    
    for i = 1 to n: 
    	num = nums[i - 1] 
    	for j = 0 to diff: 
    		dp[i][j] = dp[i - 1][j] 
    		if j >= num: 
    			dp[i][j] += dp[i - 1][j - num] 
    return dp[n][diff]
```



### 时间复杂度

最开始求和，需要O(n)的时间，主体部分，外层循环n次，内层循环diff次，因此总的时间复杂度为$O(n \times diff)$



# 三、贪心

## Q1 Commando War

### 题目

There is a war and it doesn’t look very promising for your country. Now it’s time to act. You have a commando squad at your disposal and planning an ambush on an important enemy camp located nearby. You have N soldiers in your squad. In your master-plan, every single soldier has a unique responsibility and you don’t want any of your soldier to know the plan for other soldiers so that everyone can focus on his task only. In order to enforce this, you brief every individual soldier about his tasks separately and just before sending him to the battlefield. You know that every single soldier needs a certain amount of time to execute his job. You also know very clearly how much time you need to brief every single soldier. Being anxious to finish the total operation as soon as possible, you need to find an order of briefing your soldiers that will minimize the time necessary for all the soldiers to complete their tasks. You may assume that, no soldier has a plan that depends on the tasks of his fellows. In other words, once a soldier begins a task, he can finish it without the necessity of pausing in between.

**Input**: There will be multiple test cases in the input file. Every test case starts with an integer N$(1 \leq N \leq 1000)$, denoting the number of soldiers. Each of the following N lines describe a soldier with two integers B(1 <= B <= 10000)&J(1 <= J <= 10000). B seconds are needed to brief the soldier while completing his job needs J seconds. The end of input will be denoted by a case with N = 0 . This case should not be processed.

**Output**: For each test case, print a line in the format, “Case X: Y”, where X is the case number &Y is the total number of seconds counted from the start of your first briefing till the completion of all jobs.

### 思路

**贪心规则**：优先选择所需时间最长的工作

- 所需时间相同时，先选择安排工作时间长的



## 伪代码

```python
function briefingTime(soldiers)
    sort soldiers by B in ascending order

    totalBriefingTime = 0
    totalTime = 0

    for i = 1 to N
        totalTime += soldiers[i].B
        totalBriefingTime = max(totalTime + soldiers[i].J, totalBriefingTime)

    return totalBriefingTime
```



## 时间复杂度

首先需要进行一个排序，所需时间复杂度为$O(n\log n)$，接下来进行了一层遍历，每次遍历中进行的运算为常数级，故总的时间复杂度为$O(n\log n)$



## Q2 DNA Consensus String

### 题目

 The Hamming distance is the number of different characters at each position from two strings of equal length. For example, assume we are given the two strings “AGCAT” and “GGAAT.” The Hamming distance of these two strings is 2 because the 1st and the 3rd characters of the two strings are different. Using the Hamming distance, we can define a representative string for a set of multiple strings of equal length. Given a set of strings $S = \{s_1,\dots,s_m\}$ of length n, the consensus error between a string y of length n and the set S is the sum of the Hamming distances between y and each si in S. If the consensus error between y and S is the minimum among all possible strings y of length n, y is called a consensus string of S. For example, given the three strings “AGCAT” “AGACT” and “GGAAT” the consensus string of the given strings is “AGAAT” because the sum of the Hamming distances between “AGAAT” and the three strings is 3 which is minimal. (In this case, the consensus string is unique, but in general, there can be more than one consensus string.) We use the consensus string as a representative of the DNA sequence. For the example of Figure 1 above, a consensus string of gene X is “GCAAATGGCTGTGCA” and the consensus error is 7.
$$
\begin{array}{l|l} 
& \text { DNA sequence of gene X } \\
\hline \text { Cat: } & \text { GCATATGGCTGTGCA } \\
\text { Dog: } & \text { GCAAATGGCTGTGCA } \\
\text { Horse: } & \text { GCTAATGGGTGTCCA } \\
\text { Cow: } & \text { GCAAATGGCTGTGCA } \\
\text { Monkey: } & \text { GCAAATCGGTGAGCA }
\end{array}
$$
**Input**: Your program is to read from standard input. The input consists of T test cases. The number of test cases T is given in the first line of the input. Each test case starts with a line containing two integers m and n which are separated by a single space. The integer $m(4 \leq m \leq 50)$ represents the number of DNA sequences and $n(4 \leq n \leq 1000)$ represents the length of the DNA sequences, respectively. In each of the next m lines, each DNA sequence is given.

**Output**: Your program is to write to standard output. Print the consensus string in the first line of each case and the consensus error in the second line of each case. If there exists more than one consensus string, print the lexicographically smallest consensus string.



### 思路

**贪心规则**：对于每一位上的字母，统计输入的各个字符串，得到每一位上出现频次最高的字母，若有相同则按照字典序取较低的（对于本题即A-C-G-T）



### 伪代码

```python
function consensus(sequences):
    result = ""
    
    for i = 1 to len(sequences[0]):
        charMap = createEmptyMap()
        
        for sequence in sequences:
            charMap.put(sequence[i], charMap.get(sequence[i]))
            
        sort charMap by value in ascending order
        result += charMap.get(0).key
        
    return result
```



### 时间复杂度

外层循环有n次，即每个序列的长度；内层循环有m次，即序列的数量。每一次外层循环中包含m次map读写以及一次排序，即$m + O(4 \log 4)$，由于实际上只有4中碱基，因此排序的元素永远只有四个，这一步可以认为是常数级别。故总时间复杂度为$O(nm)$



## Q3 Opponents

### 题目

Arya has n opponents in the school. Each day he will fight with all opponents who are present this day. His opponents have some fighting plan that guarantees they will win, but implementing this plan requires presence of them all. That means if one day at least one of Arya’s opponents is absent at the school, then Arya will beat all present opponents. Otherwise, if all opponents are present, then they will beat Arya.

For each opponent Arya knows his schedule — whether or not he is going to present on each particular day. Tell him the maximum number of consecutive days that he will beat all present opponents.

Note that if some day there are no opponents present, Arya still considers he beats all the present opponents.

**Input**: The first line of the input contains two integers $n$ and $d ( 1\leq n,d \leq 100 )$ — the number of opponents and the number of days, respectively. The i-th of the following d lines contains a string of length n consisting of characters ’0’ and ’1’. The j-th character of this string is ’0’ if the j-th opponent is going to be absent on the i-th day.

**Output**: Print the only integer — the maximum number of consecutive days that Arya will beat all present opponents.

### 思路

- **分析**：每天有两种情况，对手到齐或没有到齐，也就是对应着赢或输
- **贪心规则**：统计当前连胜天数，并与历史连胜天数比较，取其中最大的即可



### 伪代码

```python
function win(opponents):
    max_win = 0
    now_win = 0
    
    for i = 1 to len(opponents):
        if opponents[i] == 1:
            now_win = 0
        else:
            now_win += 1
            max_win = max(max_win, now_win)
    
    return max_win
```



### 时间复杂度

仅需要一次遍历即可得出结果，因此时间复杂度为$O(n)$



## Q4 Minimum Varied Number

### 题目

Find the minimum number with the given sum of digits s such that all digits in it are distinct (i.e. all digits are unique).

For example, if s = 20 , then the answer is 389 . This is the minimum number in which all digits are different and the sum of the digits is 20 ( 3+8+9 = 20 ).

For the given s print the required number.

**Input**: The first line contains an integer $t ( 1 \leq t \leq 45 )$ — the number of test cases. Each test case is specified by a line that contains the only integer $s ( 1 \leq s \leq 45 )$.

**Output**: Print t integers — the answers to the given test cases.



### 思路

- **分析**：实际上就是需要在1到9的数字间选取最少的数使得和为S，那么从大到小选取即可
- **贪心规则**
  - 若s小于等于9，则结果就是s本身
  - 若s大于9，则从9开始遍历，依次加上能加的最大数



### 伪代码

```python
def MIN_NUMBER(int s):
    result = ''
    last_max = 9
    
    while s>last_max:
        s -= last_max
        result = f'{last_max}{result}'
        last_max--
        
    return f'{s}{result}'
```



### 时间复杂度

由于这一算法必然能够在9次以内得到结果，因此时间复杂度为$O(C)$



## Q5 Joey Takes Money

Joey is low on money. His friend Chandler wants to lend Joey some money, but can’t give him directly, as Joey is too proud of himself to accept it. So, in order to trick him, Chandler asks Joey to play a game.

In this game, Chandler gives Joey an array $a_1,a_2,\dots,a_n ( n \geq 2 )$ of positive integers $( a_i \geq 1 )$. Joey can perform the following operation on the array any number of times:

1. Take two indices i and j $( 1 \leq i < j \leq n)$ .
2. Choose two integers x and y $( x,y \geq 1 )$ such that $x\cdot y =a_i \cdot a_j$ .
3. Replace $a_i$ by $x$ and $a_j$ by $y$ .

In the end, Joey will get the money equal to the sum of elements of the final array.

Find the maximum amount of money ans Joey can get but print $2022 \cdot ans$ . Why multiplied by 2022 ? Because we are never gonna see it again!

It is guaranteed that the product of all the elements of the array a doesn’t exceed $10^{12}$ .

**Input**: Each test contains multiple test cases. The first line contains the number of test cases $t ( 1 \leq t \leq 4000 )$. Description of the test cases follows. The first line of each test case contains a single integer $n ( 2 \leq n \leq 50 )$ — the length of the array a . The second line contains n integers $a_1, a_2,\dots,a_n ( 1 \leq a_i \leq 10^6 )$ — the array itself. It’s guaranteed that the product of all ai doesn’t exceed $10^{12} (i. e. a_1 \cdot a_2 \cdot \dots \cdot a_n \leq 10^{12} )$.

**Output**: For each test case, print the maximum money Joey can get multiplied by 2022 .



### 思路

- **分析**：
  - 这道题的目的就是对于array中的数对，选取它们的相等因子替换，使得得到的总和最大
  - 而两个数相乘的所有因子中，合最大的就是1和乘积本身
  - 因此依次将所有数全部换为乘积和1，则可以得到最大和
- **贪心规则**：对所有数相乘即可



### 伪代码

```python
def MAX_MONEY(int[] array):
    for num in array:
        result *= num
    return result + len(array) - 1
```



### 时间复杂度

由于仅需要一次遍历连乘，因此时间复杂度为$O(n)$