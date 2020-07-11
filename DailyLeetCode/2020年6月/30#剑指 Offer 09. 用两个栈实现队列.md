**[剑指 Offer 09. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)**


### 解题思路
这题与题目：[232. 用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)一样

参考之前写的[题解：232. 两个栈相互合作实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/solution/232-liang-ge-zhan-xiang-hu-he-zuo-shi-xian-dui-lie/)

相似题目：[225. 用队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)


### 代码

```python3
class CQueue:

    def __init__(self):
        self.queue = []
        self.help = []


    def appendTail(self, value: int) -> None:
        self.queue.append(value)


    def deleteHead(self) -> int:
        if self.help == []:
            if self.queue == []:
                return -1
            while self.queue != []:
                self.help.append(self.queue.pop())
        return self.help.pop()



# Your CQueue object will be instantiated and called as such:
# obj = CQueue()
# obj.appendTail(value)
# param_2 = obj.deleteHead()
```
