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

### 代码
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
