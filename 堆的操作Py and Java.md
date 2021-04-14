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


--------------------------
215.TopK Python3代码小总结，快排以及堆的详细实现


**[215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)**

堆的思想可以参考官方题解的PPT

### 解题思路

代码1：直接调用库函数，库函数的快速排序还是快啊~
时间复杂度：O(nlogn)，n为数组长度，并不需要将整个数组排序
空间复杂度：O(1)


代码2：使用快速排序思想，用partition方法，每次将一个数定位到自己应该在的位置
代码参考[liweiwei1419题解](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/partitionfen-er-zhi-zhi-you-xian-dui-lie-java-dai-/)，自己实现了一遍。
时间复杂度：O(n)，n为数组长度，因为并不需要将整个数组排序
空间复杂度：O(1)

代码3：在思路2的基础上进行优化，思路2的pivot是选择的第一个元素，这里改成选择随机元素。即在一开始，将第一个元素随机的与后面的任意一个元素交换。这样的改进方法避免了极端情况，时间明显变快。

代码4：Python内置堆方法，建立小根堆

代码5：同代码4，使用heappushpop

代码6：Python内置堆方法，建立大根堆

**代码7：Python3大根堆的详细实现**

- 代码4-7可以认为：`Time:O(n + klgn)、Space: O(k)`，n为数组长度，k为第k大的值的k

### 代码

```python3

# 代码1：暴力排序
# Time: O(nlogn)
# Space: O(1)
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        nums.sort(reverse=True)
                return nums[k-1]


# 代码2：快排思想选择第k大元素
# Time: O(n)，极端情况下退化到O(n^2)
# Space: O(1)
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        size = len(nums)  
        target = size - k
        left = 0
        right = size - 1
        while True:
            index = self.partition(nums, left, right)
            if index == target:
                return nums[target]
            elif index < target:
                left = index + 1
            else:
                right = index - 1
            
        
    def partition(self, nums, left, right):
        pivot = nums[left]
        j = left
        for i in range(left+1, right+1):
            if nums[i] < pivot:
                j += 1
                nums[i], nums[j] = nums[j], nums[i]
        nums[left], nums[j] = nums[j], nums[left]
        return j


# 代码3：加入随机选择元素的快排方法
# Time: O(n)，随机选择避免极端情况
# Space: O(1)
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        size = len(nums)  
        target = size - k
        left = 0
        right = size - 1
        while True:
            index = self.partition(nums, left, right)
            if index == target:
                return nums[target]
            elif index < target:
                left = index + 1
            else:
                right = index - 1
            
        
    def partition(self, nums, left, right):
        random_idx = random.randint(left, right)
        nums[random_idx], nums[left] = nums[left], nums[random_idx]
        
        pivot = nums[left]
        j = left
        for i in range(left+1, right+1):
            if nums[i] < pivot:
                j += 1
                nums[i], nums[j] = nums[j], nums[i]
        nums[left], nums[j] = nums[j], nums[left]
        return j


# 代码4：Python内置堆方法，建立小根堆
# heapq.heapreplace(heap, item)弹出并返回heap中最小的一项，同时推入新的item
# Time:O(n + klgn)
# Space: O(k)
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        heap = nums[:k]
        heapq.heapify(heap)
        for num in nums[k:]:
            if num > heap[0]:
                heapq.heapreplace(heap, num)
        return heap[0]


# 代码5：同代码4，使用heappushpop
# heapq.heappushpop(heap, item)将item放入堆中，然后弹出并返回 heap 的最小元素
# heappushpop与heapreplace区别就是heappushpop弹出的元素不会比添加的元素大
# Time:O(n + klgn)
# Space: O(k)
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        heap = nums[:k]
        heapq.heapify(heap)  # O(n)
        for num in nums[k:]:
            heapq.heappushpop(heap, num)  # O(klgn)
        return heap[0]


# 代码6：Python内置堆方法，建立大根堆
# Time:O(n + klgn)
# PS：这里的时间复杂度其实按照大了说的
# 这种情况是Push和Pop分开的复杂度
# 内置函数可以将这两个函数放在一起来减少复杂度
# 如代码4、5的两个函数，和代码6的这个
# 真实时间复杂度其实更低，是O(k + (n-k) log k)
# 具体可参考维基百科或者堆的API：https://docs.python.org/zh-cn/3/library/heapq.html
# Space: O(k)
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:  # Time: O(k + (n-k) log k), space: O(k)
        ans = heapq.nlargest(k, nums) # run time O(n+klgn)
        return ans[-1]


# 代码7：Python3大根堆的详细实现
# Time:O(n + klgn)
# Space: O(k)
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:  # Time: O(k + (n-k) log k), space: O(k)
        
        def maxHeapify(nums, i):  # 递归不断修正大根堆
            largest = i           # 当前最大值索引默认为根节点索引i
            l = i * 2 + 1         # 左孩子索引
            r = i * 2 + 2         # 右孩子索引
            if l < len(nums) and nums[l] > nums[largest]:
                largest = l       # 如果左孩子值大于根节点，存储左孩子索引
            if r < len(nums) and nums[r] > nums[largest]:
                largest = r       # 如果右孩子值大于根节点，存储右孩子索引
            if largest != i:      # 递归终止条件，只在largest被左或右孩子更新的时候才递归
                nums[i], nums[largest] = nums[largest], nums[i]  # 左右孩子中大的值上浮
                maxHeapify(nums, largest)
        
        def buildMaxHeap(nums):                        # 将一个列表转为大根堆
            for i in range(len(nums) // 2 - 1, -1, -1):# 从最后一个非叶子节点开始向前遍历
                maxHeapify(nums, i)
                
        def findKthMax(nums, k):                       # 堆排序找到第k大元素
            buildMaxHeap(nums)                         # 建立大根堆
            for _ in range(k - 1):
                nums[0], nums[-1] = nums[-1], nums[0]  # 交换堆顶和堆尾元素，最大值沉底
                nums.pop()                             # O(1)时间删除最大值，防止最大值再次上浮
                maxHeapify(nums, 0)                    # 从堆顶修正大根堆
            return nums[0]
        
        return findKthMax(nums, k)

```

----------

Java实现堆排序(大根堆)
```java
package Sort;

import java.util.Arrays;

/**
 * @author Orust
 * @create 2021/4/13 21:44
 */
public class HeapSort {

    public static void main(String[] args) {
        HeapSort Sort = new HeapSort();
        int[] nums = {5, 1, 1, 2, 0, 0, 9, 4, 3};
//        int[] nums = {1, 2, 3, 0, 1, 0, 0, 1, 2, 3};
        Sort.heapSort(nums);
        System.out.println(Arrays.toString(nums));
    }

    // 每次maxHeapify选出堆的根节点，即最大值，与数组的最后一个值交换
    public void heapSort(int[] nums) {
        for (int i = nums.length / 2 - 1; i >= 0; i--)
            maxHeapify(nums, i, nums.length);
        for (int i = nums.length - 1; i >= 0; i--) {
            swap(nums, 0, i);
            maxHeapify(nums, 0, i);
        }
    }

//    // 尾递归实现建堆
//    public void maxHeapify(int[] nums, int i, int len) {
//        int largest = i;  // i 需要构建堆的根节点的序号 len 当前需要排序的数组长度
//        int l = i * 2 + 1;
//        int r = i * 2 + 2;
//        if (l < len && nums[l] > nums[largest])
//            largest = l;
//        if (r < len && nums[r] > nums[largest])
//            largest = r;
//        if (largest != i) {
//            swap(nums, i, largest);
//            maxHeapify(nums, largest, len);
//        }
//    }

    // 循环实现建堆1
    public void maxHeapify(int[] nums, int i, int len) {
        while(true){
            int largest = i;  // i 需要构建堆的根节点的序号 len 当前需要排序的数组长度
            int l = i * 2 + 1;
            int r = i * 2 + 2;
            if (l < len && nums[l] > nums[largest])
                largest = l;
            if (r < len && nums[r] > nums[largest])
                largest = r;
            if (largest == i)
                return;
            swap(nums, i, largest);
            i = largest;
        }
    }

//    // 循环实现建堆2
//    public static void maxHeapify(int[] nums, int i, int len) {
//        int temp = nums[i];
//        for (int k = 2 * i + 1; k < len; k = 2 * i + 1) {
//            if (k + 1 < len && nums[k] < nums[k + 1])
//                k++;
//            if (temp < nums[k]) {
//                nums[i] = nums[k];
//                i = k;
//            } else break;
//        }
//        nums[i] = temp;
//    }

    public void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}


```

