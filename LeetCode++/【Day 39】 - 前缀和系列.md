[前缀和的总结](https://github.com/Liu821218213/LeetCode-Orust/blob/master/%E5%89%8D%E7%BC%80%E5%92%8C%E7%9A%84%E6%80%BB%E7%BB%93.md)
------------------------------------------
网易面试题(未做)
```
有一个班级有 n 个人，给出 n 个元素，第 i 个元素代表 第 i 位同学的考试成绩，接下进行 m 次询问，
每次询问给出一个数值 t ，表示第 t 个同学，然后需要我们输出第 t 个同学的成绩超过班级百分之几的人，
百分数 p 可以这样算：p = (不超过第 t 个同学分数的人数 ) / n * 100%。输出的时候保留到小数点后 6 位，并且需要四舍五入。

输入描述：第一行输入两个数 n 和 m，两个数以空格隔开，表示 n 个同学和 m 次询问。第二行输入 n 个数值 ni，
表示每个同学的分数，第三行输入 m 个数值mi，表示每次询问是询问第几个同学。（注意，这里 2<=n，m<=100000，0<=ni<=150，1<=mi<=n）

输出描述：输出 m 行，每一行输出一个百分数 p，代表超过班级百分之几的人。

示例1：

输入 ：
3 2
50 60 70
1 2

输出
33.333333%
66.666667%
```


------------------------

其他前缀和题目：308、525、1139、1176、1182、1277、1292、1314、1504

**[560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)**


### 解题思路
方法1：
刚开始用前缀和，先计算前n项的和，然后在前缀和数组中找第j项与第i项之间的差值看是否等于k
这种方法超时

方法2：
可以采用hash表的方式来降低时间复杂度，在求前n项和的同时，用一个字典记录当前和出现的次数，
同时检查`s - k`是否在字典中，如果在，进行计数。
注意，字典初始化的时候需要先初始化一个前缀和为0的个数为1，因为下标0之前没有元素，计算前n项和的时候，当某个前n项和刚好为k的时候，也需要计数。

### 代码

```python3
# 方法1：前缀和数组，超时
# 时间O(n^2)，空间O(n)，n为数组长度
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        n = len(nums)
        
        s = [0] * (n + 1)
        for i in range(1, n + 1):
            s[i] = s[i - 1] + nums[i - 1]
        # print(s)
        count = 0
        for i in range(0, n):
            for j in range(i + 1, n + 1):
                if s[j] - s[i] == k:
                    count += 1
        return count


# 方法2：前缀和 + hash表
# 时间O(n)，空间O(n)，n为数组长度
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = defaultdict(int)
        count[0] = 1
        s, cnt = 0, 0
        for num in nums:
            s += num 
            if s - k in count:
                cnt += count[s - k]
            count[s] = count[s] + 1 if s in count else 1 
        return cnt

```

# ------------------------------分割线----------------------------------


**[1371. 每个元音包含偶数次的最长子字符串](https://leetcode-cn.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/)**


### 解题思路
异或+前缀和+Hash。
有点可惜，前缀和思想的应用并没有熟练掌握，但是收获更多，继续加油吧。

这段时间做题的原因，前两天做的异或运算的题目，之前做的hash表操作，这两天又刚接触了前缀和。
所以看到这题的奇偶次数判断，很敏感的想到了用异或运算（平时可想不出来）。

起初我想把这五个元音字母用数字代替，非元音字母用-1代替，这样比较好分析一点。但是最后发现并没有必要，可以将每个字符转化为ascill码来判断。
判断元音和非元音，建一个字典。

判断连续串的长度，虽然知道是用前缀和，因为这题的特征，
和[560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)的使用前缀和的特征非常的相似，可以借助hash表来判断这个时候是否需要计数。

但是想法就到这里，前缀和与hash表的配合并没有实现出来，看了评论区才写出。

本题知识点：
- 异或运算
- hash表
- 前缀和
- 前缀和+hash

注意，这题的前缀和是异或运算的前缀和，初始的时候要先初始化一个`res = {0:-1}`，这保证第一次遇到目标时，能够有结果，这与560题的操作如出一辙。
这也应该是前缀和的通用操作。
就是这里没想明白导致结果老是错，以为是自己的方向错了。

这题的时间复杂度就是O(n)，
而空间复杂度，只有五个元音字母及其异或产生的字典的空间(两个常量空间的字典)，O(2^5)，应该不算O(n)的空间。

有时间再总结吧。

### 代码
```python3
class Solution:
    def findTheLongestSubstring(self, string: str) -> int:
        vowel = {'a', 'e', 'i', 'o', 'u'}
        res = {0:-1}
        ans, cur = 0, 0
        for i, s in enumerate(string):
            if s in vowel:
                cur ^= ord(s)
            if cur in res:
                ans = max(ans,i - res[cur])
            else:
                res[cur] = i
        return ans
```
