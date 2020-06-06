
转自:

https://blog.csdn.net/Baoli1008/article/details/48059623

http://www.orangecube.net/python-time-complexity

Python内置方法的时间复杂度
================

本文翻译自[Python Wiki](https://wiki.python.org/moin/TimeComplexity)  
本文基于[GPL v2](http://moinmo.in/GPL "MoinMoin is GPL licensed.")协议，转载请保留此协议。

本页面涵盖了Python中若干方法的时间复杂度（或者叫“大欧”，“Big O”）。该时间复杂度的计算基于当前（译注：至少是2011年之前）的CPython实现。其他Python的实现（包括老版本或者尚在开发的CPython实现）可能会在性能表现上有些许小小的差异，但一般不超过一个O(log n)项。

本文中，’n’代表容器中元素的数量，’k’代表参数的值，或者参数的数量。

### 列表（`list`）

以完全随机的列表考虑平均情况。

列表是以数组（Array）实现的。最大的开销发生在超过当前分配大小的增长，这种情况下所有元素都需要移动；或者是在起始位置附近插入或者删除元素，这种情况下所有在该位置后面的元素都需要移动。如果你需要在一个队列的两端进行增删的操作，应当使用`collections.deque`（双向队列）

|操作	|平均情况	|[最坏情况](https://en.wikipedia.org/wiki/Amortized_analysis)|
| -------- | -------- | -------- |
|复制	|O(n)|O(n)|
|append[注1]	|O(1)	|O(1)|
|插入	|O(n)	|O(n)|
|取元素	|O(1)|	O(1)|
|更改元素|	O(1)	|O(1)|
|删除元素	|O(n)	|O(n)|
|遍历|	O(n)	|O(n)|
|取切片	|O(k)	|O(k)|
|删除切片	|O(n)	|O(n)|
|更改切片	|O(k+n)	|O(k+n)|
|extend[注1]	|O(k)|O(k)|
|[排序](http://svn.python.org/projects/python/trunk/Objects/listsort.txt)	|O(n log n)	|O(n log n)|
|列表乘法	|O(nk)	|O(nk)|
|x in s	|O(n)	 ||
|min(s), max(s)	|O(n)	| |
|计算长度	|O(1)	|O(1)|


### 双向队列（`collections.deque`）

deque （double-ended queue，双向队列）是以双向链表的形式实现的 (Well, a list of arrays rather than objects, for greater efficiency)。双向队列的两端都是可达的，但从查找队列中间的元素较为缓慢，增删元素就更慢了。

|操作	|平均情况	|[最坏情况](https://en.wikipedia.org/wiki/Amortized_analysis)|
| -------- | -------- | -------- |
|复制|	O(n)	|O(n)|
|append	|O(1)	|O(1)|
|appendleft| O(1)	|O(1)|
|pop	|O(1)	|O(1)|
|popleft	|O(1)	|O(1)|
|extend	|O(k)	|O(k)|
|extendleft	|O(k)	|O(k)|
|rotate	|O(k)	|O(k)|
|remove	|O(n)	|O(n)|


### 集合（set）

未列出的操作可参考 dict —— 二者的实现非常相似。

|操作	|平均情况	|[最坏情况](https://en.wikipedia.org/wiki/Amortized_analysis)|
| -------- | -------- | -------- |
|x in s	|O(1)	|O(n)|
|并集 sIt	|O(len(s)+len(t))	 ||
|交集 s&t	|O(min(len(s), len(t))	|O(len(s) * len(t))|
|差集 s-t	|O(len(s))	 ||
|s.difference_update(t)|	O(len(t))	 ||
|对称差集 s^t	|O(len(s))|	O(len(s) * len(t))|
|s.symmetric_difference_update(t)	|O(len(t))	|O(len(t) * len(s))|


由源码得知，求差集（`s-t`，或`s.difference(t)`）运算与更新为差集（`s.difference_uptate(t)`）运算的时间复杂度并不相同！前者是将在s中，但不在t中的元素添加到新的集合中，因此时间复杂度为O(len(s))；后者是将在t中的元素从s中移除，因此时间复杂度为O(len(t))。因此，使用时请留心，根据两个集合的大小以及是否需要新集合来选择合适的方法。

集合的s-t运算中，并不要求t也一定是集合。只要t是可遍历的对象即可。

### 字典（dict）

下列字典的平均情况基于以下假设：  
1\. 对象的散列函数足够撸棒（robust），不会发生冲突。  
2\. 字典的键是从所有可能的键的集合中随机选择的。

小窍门：只使用字符串作为字典的键。这么做虽然不会影响算法的时间复杂度，但会对常数项产生显著的影响，这决定了你的一段程序能多快跑完。

|操作	|平均情况	|[最坏情况](https://en.wikipedia.org/wiki/Amortized_analysis)|
| -------- | -------- | -------- |
|复制[注2]|	O(n)	|O(n)|
|取元素|	O(1)	|O(n)|
|更改元素[注1]|	O(1)	|O(n)|
|删除元素|	O(1)	|O(n)|
|遍历[注2]	|O(n)	|O(n)|

注：  
\[1\] = These operations rely on the “Amortized” part of “Amortized Worst Case”. Individual actions may take surprisingly long, depending on the history of the container.

这些操作依赖于“最坏情况摊销”的“摊销”部分。根据容器的历史记录，各个动作可能会花费很长的时间。

\[2\] = For these operations, the worst case n is the maximum size the container ever achieved, rather than just the current size. For example, if N objects are added to a dictionary, then N-1 are deleted, the dictionary will still be sized for N objects (at least) until another insertion is made.

对于这些操作，最坏的情况n是容器曾经达到的最大大小，而不仅仅是当前大小。 例如，如果将N个对象添加到词典中，然后删除N-1个，则该词典仍将为N个对象调整大小（至少），直到再次插入为止。
