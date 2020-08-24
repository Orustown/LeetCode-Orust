**[746. 使用最小花费爬楼梯](https://leetcode-cn.com/problems/min-cost-climbing-stairs/)**



### 解题思路
在有花费的爬楼梯问题中，使得花费最少：

状态转移公式可以很容易的得到：

`dp[i] = min(dp[i - 1], dp[i - 2]) + cost[i]`

而问题可能会变化：

#### 爬楼梯的变型题1：
[746. 使用最小花费爬楼梯](https://leetcode-cn.com/problems/min-cost-climbing-stairs/)

本题需要将最后一层走完（可以不走最后一层），而开始的时候可以选择从第一层还是第二层开始，题目规定层数>=2

因此本题在初始化前两层dp后，遍历赋值即可，注意最后返回的是最后一层后倒数第二层中的最小值，因为可以不走最后一层。


#### 爬楼梯的变型题2：
```
已知有一个 n 级的台阶，我们的目标是走到第 n 级，但是踩到不同的台阶需要耗费不同的你能量，我们用 enegy 数组 来表示。 其中 enegy[i] 表示爬到 第 i 级台阶需要掉的能量。我们可以一次走一级或者一次走两级。 求走到第 n 级需要的最少能量。

输入：
[1,2,3,4]

输出:
6

解释：
我们先一次走两级，走到 i = 1 的位置，此时需要耗费 2 的能量，接着我们再走两级，此时需要 4。 2 + 4 = 6
```

本题则规定了必须要走最后一层，而也没有限定层数>=2，因此变化就是：先考虑层数<=2的情况，然后在返回的时候返回最后一层的dp值即可，因为最后一层是必须走的。

其实这个变化和变型题1是很明显的，可以看作是变型题1的基础上，再多走一层。


### 代码

```python3
# 爬楼梯的变型题1
class Solution:
    def minCostClimbingStairs(self, cost: List[int]) -> int:
        n = len(cost)
        dp = [0] * n
        dp[0] = cost[0]
        dp[1] = cost[1]
        for i in range(2, n):
            dp[i] = min(dp[i - 1], dp[i - 2]) + cost[i]
        return min(dp[-2], dp[-1])
        


# 爬楼梯的变型题2
class Solution:
    def minCostClimbingStairs_2(self, cost: List[int]) -> int: 
        n = len(cost)
        if n <= 2:
            return cost[-1]
        dp = [0] * n
        dp[0] = cost[0]
        dp[1] = cost[1]
        for i in range(2, n):
            dp[i] = min(dp[i - 1], dp[i - 2]) + cost[i]
        return dp[-1]
        
```
