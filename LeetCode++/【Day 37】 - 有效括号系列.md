**[20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)**


### 解题思路
方法1：纯粹的进出栈判断

学习一下评论区
方法2：[@wings-kbftQ3BYHH](/u/wings-kbftq3byhh/) 的删除匹配字符串的做法
以及
方法3：[@old-wang](/u/old-wang/) 的字典做法

方法4 and 方法5：
2020/7/6 更新了两种空间复杂度O(1)的做法方法4、5。(注：方法2也是空间复杂度O(1)的做法，不过这是参考的别人的)

详细思路见代码注释
如有错误请大佬指出

### 代码

```python3
# 方法1：自己的第一思路，用栈即可，最普通的做法
# 时间：O(n)，n为字符串长度
# 空间：O(n)
class Solution:
    def isValid(self, s: str) -> bool:
        if s == "": return True
        if s[-1] == '(' or s[-1] == '{' or s[-1]=='[': return False
        
        stack = []
        for a in s:
            if a == '(' or a == '{' or a=='[':
                stack.append(a)
            else:
                if stack:
                    b = stack.pop()
                    if b == '(':
                        if a != ')':
                            return False
                    if b == '{':
                        if a != '}':
                            return False
                    if b == '[':
                        if a != ']':
                            return False
                else:
                    return False
        return len(stack) == 0
    

            
# 方法2：删除匹配字符串的做法
# 时间：不想分析了
# 空间：O(1)
class Solution:
    def isValid(self, s):
        while '{}' in s or '()' in s or '[]' in s:
            s = s.replace('{}', '')
            s = s.replace('[]', '')
            s = s.replace('()', '')
        return s == ''



# 方法3：字典做法
# 时间：O(n)
# 空间：O(n)
class Solution(object):
    def isValid(self, s):
        if len(s) % 2 == 1:  # 奇数长度
            return False
        
        d = {'(': ')', '{': '}', '[': ']'}
        stack = []
        for char in s:
            if char in d:  # 左括号入栈
                stack.append(char)
            else:
                if not stack or d[stack.pop()] != char:  # 没入栈就出栈 或 出栈元素不匹配当前元素
                    return False

        return True if not stack else False


# 方法4：用指针模拟栈，修改原字符串
# PS：由于Python的字符串是不可修改的类型，将它先转为数组字符串了，为了方便操作
# 但是可以用s.replace()来替代这个操作，或者用python切片的操作，但是这样时间复杂度就会很高了
# 时间：O(n)
# 空间：O(1)
class Solution:
    def isValid(self, s: str) -> bool:
        s = list(s)
        d = {'(': ')', '{': '}', '[': ']'}
        for i in range(len(s)):
            if s[i] == ')' or s[i] == '}' or s[i] == ']':
                flag = i - 1
                while flag >= 0 and s[flag] == '#':
                    flag -= 1
                if flag < 0: return False
                if s[flag] not in d: return False
                if d[s[flag]] == s[i]:
                    s[flag] = '#'
                    s[i] = '#'
        for v in s:
            if v != '#':
                return False
        return True

                    
# 方法5：原地修改原字符串
# PS：由于Python的字符串是不可修改的类型，将它先转为数组字符串了，为了方便操作
# 但是可以用s.replace()来替代这个操作，或者用python切片的操作，但是这样时间复杂度就会很高了
# 时间：O(n)
# 空间：O(1)
class Solution:
    def isValid(self, s: str) -> bool:
        s = list(s)
        d = {'(': ')', '{': '}', '[': ']'}
        n = len(s)
        i = 0
        flag = -1
        while i < n:
            if s[i] == '(' or s[i] == '{' or s[i] == '[':
                flag += 1
                s[flag] = s[i]
                i += 1
            elif flag >= 0 and d[s[flag]] == s[i]:
                flag -= 1
                i += 1
            else:
                return False
        return flag == -1
```
------------------------------
#  ---------------------- 我是一道分割线----------------------------

------------------------------

32题是前两天的官方每日一题，当时参考的官方题解，用DP做出来的


**[32. 最长有效括号](https://leetcode-cn.com/problems/longest-valid-parentheses/)**


### 解题思路

方法1：DP，时间O(n)，空间O(n)，DP方法参考[官方题解](https://leetcode-cn.com/problems/longest-valid-parentheses/solution/zui-chang-you-xiao-gua-hao-by-leetcode-solution/)

方法2：两轮循环，时间O(n)，空间O(1)
因为这题只有两种字符串，所以采用计数方式判断非常的容易
从左向右，计算当前左右括号的数量，当右括号数大于左括号数的时候，重新计数，因为这个时候不满足一个合格的括号串
不断记录合格的最大括号串
这样计算的到的结果是不完整的，因为可能存在左括号数大于右括号的情况
所以我们从右向左在进行一遍，最后的得到的结果一定是最长的括号串


方法3：本题还有栈的方法，还不会


### 代码
```python3
# 方法1：DP
# 时间：O(n)
# 空间：O(n)
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        n = len(s)
        dp = [0] * n
        for i in range(1, n):
            if s[i] == ')':
                pre = i - 1 - dp[i-1]
                if pre >= 0 and s[pre] == '(':
                    dp[i] = dp[i-1] + 2
                    if pre > 0:
                        dp[i] += dp[pre-1]
        print(dp)
        return max(dp) if n !=0 else 0

# 方法2：正反向，两轮循环取最大值
# 时间：O(n)
# 空间：O(1)
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        n = len(s)
        ans = 0
        i = j = 0
        for k in range(n):
            if s[k] == '(':
                i += 1
            else:
                j += 1
                if j > i:
                    i = j = 0
            if i == j:
                ans = max(ans, i + j)
        # print(ans)
        i = j = 0
        for k in range(n-1, -1, -1):
            if s[k] == ')':
                i += 1
            else:
                j += 1
                if j > i:
                    i = j = 0
            if i == j:
                ans = max(ans, i + j)
        return ans
                
```
