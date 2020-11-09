**[973. 最接近原点的 K 个点](https://leetcode-cn.com/problems/k-closest-points-to-origin/)**

其他相关题目总结参考
[【Day 56 - 61】08.堆](https://github.com/Liu821218213/LeetCode-Orust/tree/master/LeetCode%2B%2B)



### 解题思路
本题考察堆排序

Python3中堆的一些操作如下

```Python3
import heapq
heap = []

heapq.heappush(heap, (distance, point)) # 向堆heap中插入数据，以distance为第一参考值，point为第二参考值（如果需要的话）

heapq.append((distance, point))         # 以上语句等于这两句，复杂度也一样，插入的是一个 元组()
heapq.heapify(heap)

heapq.heappop(heap)[1]                  # 弹出堆顶数据，弹出的也是一个元组，但这里取元组中的第二个数

heappushpop(heap, (distance, points[i]))  # 插入新元素并弹出堆顶元素，先插入后弹出，最后弹出的一定小于等于插入的元素
heapreplace(heap, (distance, points[i]))  # 弹出堆顶元素然后插入新元素
```

Python3默认是小根堆，即从小到大排序，先弹出的是最小值，如果想要实现大根堆，有两种方式：
1. 自己手动实现
2. 将小根堆的第一个数字取**负数**然后堆排序



### 代码
```Python3
class Solution:
    def kClosest(self, points: List[List[int]], K: int) -> List[List[int]]:
        heap = []
        for point in points:
            distance = point[0] * point[0] + point[1] * point[1]
            heappush(heap, (distance, point))
        res = []
        for i in range(K):
            res.append(heappop(heap)[1])
        return res
```

优先队列（大根堆）：
```Python3
class Solution:
    def kClosest(self, points: List[List[int]], K: int) -> List[List[int]]:
        heap = []
        for i in range(K):
            distance = - points[i][0] * points[i][0] - points[i][1] * points[i][1]
            heappush(heap, (distance, points[i]))
        
        for i in range(K, len(points)):
            distance = - points[i][0] * points[i][0] - points[i][1] * points[i][1]
            heappushpop(heap, (distance, points[i]))
        return [res[1] for res in heap]
```
改进：
```Python3
class Solution:
    def kClosest(self, points: List[List[int]], K: int) -> List[List[int]]:
        heap = []
        for i in range(len(points)):
            distance = - points[i][0] * points[i][0] - points[i][1] * points[i][1]
            if i < K:
                heappush(heap, (distance, points[i]))
            else:
                heappushpop(heap, (distance, points[i]))
        return [res[1] for res in heap]
```

