# 前缀和
第一次听到这个概念，以为是什么数据结构，但是其实就是数列前n项和的应用。
前缀和是一种重要的预处理，能大大降低查询的时间复杂度，用空间来换时间。

### 定义
前缀和可以简单的理解成数组的前n项和，我们将前n项和组成的数组称为前缀和数组。

### 前缀和公式
求解前缀和数组的思想其实就是动态规划的思想，前n项和的求解是具有最优子结构以及重叠子问题的，因此它本质就是一个DP思想，利用DP思想可以推导并计算高维的前缀和。

* 分析
数组的前n项和只与前n-1项和以及当前元素相关，与之后元素无关，这就是自底向上的DP。而数组可以有一维数组，二维数组甚至是多维数组，这些数组的前缀和数组就可以用DP的思想来求。

* 一维前缀和
DP[i] = DP[i-1] + a[i]
即最基本的数列前n项和公式。

* 二维前缀和
DP[i][j]=DP[i-1][j]+DP[i][j-1]+ａ[i][j]-DP[i-1][j-1]
可以画图来理解，在二维数组中求前n项和，将二维数组视为一个矩形，我们要求的是矩形的面积，已知前n-1项的所有面积（前缀和），求第n项的面积，即DP[i][j]。
DP[i-1][j]与DP[i][j-1]分别是两个矩形的面积，加上当前的值ａ[i][j]，这个适合我们可以发现，那两个矩形的面积有一块重叠，我们减去这个重叠的面积DP[i-1][j-1]即可得到当前的面积，即DP[i][j]，也即二维前缀和。

* 高维前缀和
高维前缀和递推参考：[基于 DP 计算高维前缀和](https://oi-wiki.org/basic/prefix-sum/#dp)
估计应用到高维的只有ACMer了吧

### 推广
与前缀和相对应的还有一种思想叫做差分，这是一种与前缀和相对的策略（暂时不去了解了）。

### 应用
前缀和可以应用到一些数组内的题目中，在树形结构中也经常应用，一般称为前缀树。

### 参考资料
前缀和以及差分的模板可以参考以下两个网站：

[OI Wiki](https://oi-wiki.org/basic/prefix-sum/#dp)

[AcWing](https://www.acwing.com/blog/content/277/)
  


# 实现Trie

**[208. 实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)**



### 解题思路
前缀树的前身其实是前缀和，将前缀和的思想应用在树形结构上，就成了前缀树。
和前缀和一样，这是一种针对字符串查找的空间换时间的高效方法。

前缀树的本身很好理解，根节点代表初始遍历结点，无实际意义。
所以我们可以将每条边视为一个路径，这个路径或者路径指向的下一个结点对应着一个字符，（任何一棵树中，除去根节点后，所有边的数量等于所有节点数）。

我们可以自定义结点的相关特征，例如到当前字符为止，是否搜索到了目标单词，或者目标前缀出现了多少次。
当整棵树都访问完也没有发现目标即可认为搜索失败。

我们初始化的时候需要定义结点意义，可用字典，也可以另外定义一个结点类、
插入结点的时候需要将结点相应的特性也定义好。

搜索字符串和判断是否是前缀，都是类似的操作。

复杂度分析：n为单词（键、字符串）的长度，m为前缀长度(m<=n)
1. 插入一个单词（键、字符串）。时间：O(n)，空间：O(n)
2. 查找一个单词（键、字符串）。时间：O(n)，空间：O(1)
3. 查找单词（键、字符串）前缀。时间：O(m)，空间：O(1)

易知，在时间上，我们只需花费当前单词（前缀）长度的时间，因为字典的查找是O(1)的时间，只需挨个遍历树的节点即可。
在空间上，只有在插入节点的时候才花费空间，且花费的空间就是所插入的单词的长度。而查找上不花费任何空间。

这只是针对插入一个单词而言，如果对应的题目中的单词长度均为`p`，字符种类数为`q`，
则最坏情况下建树的时间和空间花费均是`O(q^p)`，此时单词总数为`count = q^(p-1)`。

### Python3代码
```python3
class TrieNode:
    def __init__(self):
        self.children = defaultdict(TrieNode)
        self.is_word = False


class Trie:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.root = TrieNode()


    def insert(self, word: str) -> None:
        """
        Inserts a word into the trie.
        """
        node = self.root
        for s in word:
            node = node.children[s]
        node.is_word = True


    def search(self, word: str) -> bool:
        """
        Returns if the word is in the trie.
        """
        node = self.root
        for s in word:
            if s in node.children:
                node = node.children[s]
            else:
                return False
        return node.is_word


    def startsWith(self, prefix: str) -> bool:
        """
        Returns if there is any word in the trie that starts with the given prefix.
        """
        node = self.root
        for s in prefix:
            if s in node.children:
                node = node.children[s]
            else:
                return False
        return True
        


# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.search(word)
# param_3 = obj.startsWith(prefix)

```



### Java代码
```java
package Trie;

/**
 * @author Orust
 * @create 2021/4/14 13:52
 */
public class Trie {
    static class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean is_word = false;
    }

    TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    public void insert(String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            int idx = word.charAt(i) - 'a';
            if (node.children[idx] == null) node.children[idx] = new TrieNode();
            node = node.children[idx];
        }
        node.is_word = true;
    }

    public boolean search(String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            int idx = word.charAt(i) - 'a';
            if (node.children[idx] == null) return false;
            node = node.children[idx];
        }
        return node.is_word;
    }

    public boolean startsWith(String prefix) {
        TrieNode node = root;
        for (int i = 0; i < prefix.length(); i++) {
            int idx = prefix.charAt(i) - 'a';
            if (node.children[idx] == null) return false;
            node = node.children[idx];
        }
        return true;
    }
}
```
