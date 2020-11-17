**[1030. 距离顺序排列矩阵单元格](https://leetcode-cn.com/problems/matrix-cells-in-distance-order/)**


### 解题思路


sorted的lambda表达式排序
```python3
class Solution:
    def allCellsDistOrder(self, R: int, C: int, r0: int, c0: int) -> List[List[int]]:
        res = [[r, c] for r in range(R) for c in range(C)]
        return sorted(res, key=lambda x:abs(x[0]-r0)+abs(x[1]-c0))
```


python3中sorted函数自定义排序：
```
# 例1:
def cmp(a, b):  # 数组升序排序
    if b < a:
        return 1
    if a < b:
        return -1
    return 0
a = [1, 2, 5, 4]
print(sorted(a, key=functools.cmp_to_key(cmp)))

# 或者:
def cmp(a, b):  # 数组升序排序
    return a - b
a = [1, 2, 5, 4]
print(sorted(a, key=functools.cmp_to_key(cmp)))
```

```
# 例2:
a = [[3,1],[9,7],[6,4],[1,0],[1,3],[3,3]]
def cmp(x, y):
    # if x[0] != y[0]:        # 如果第一个元素不同
    #     return x[0] - y[0]   # 则按照第一个元素升序排序
    # else:
    #     return y[1] - x[1]   # 否则按照第二个元素降序排序
    return x[0] - y[0] if x[0] != y[0] else y[1] - x[1]
print(sorted(a,key=cmp_to_key(cmp)))
```



### 代码

```python3
class Solution:
    def allCellsDistOrder(self, R: int, C: int, r0: int, c0: int) -> List[List[int]]:
        res = [[r, c] for r in range(R) for c in range(C)]
        def cmp(x, y):      # 按d1-d2的大小升序
            d1 = abs(x[0] - r0) + abs(x[1] - c0)
            d2 = abs(y[0] - r0) + abs(y[1] - c0)
            return d1 - d2
        
        return sorted(res, key=cmp_to_key(cmp))
```