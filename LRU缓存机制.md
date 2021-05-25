## LRU缓存机制

### 前言

LRU（Least Recently Used）即最近最少使用、最近最久未使用，是一种缓存解决方案。

缓存思想在计算机科学中应用广泛，其核心是以空间换时间，实现方式主要采用特定的数据结构，在计算机存储层次结构中，低一层的存储器都可以看做是高一层的缓存。比如Cache是内存的缓存，内存是硬盘的缓存，硬盘是网络的缓存等等。

第一次接触到LRU是在操作系统的课程上，在提到OS页面调度算法的时候提到的LRU页面置换算法，页面置换是基于时间和空间局部性原理的基础上，而缓存类似于LRU这样的缓存技术在这样的场景下得到应用。

LRU在数据库缓存中也有广泛应用，例如Redis缓存淘汰策略。在使用next.js的时候，访问页面时，为了加快访问速度也会用到。

LRU：如果一个数据最近没有被访问到，那么它将来被访问的几率也很小，也就是说当限定的内存空间已没有其他空间可用时，应该把最久没有访问到的数据去除掉。

![img](https://raw.githubusercontent.com/Liu821218213/Typora-PicGo/master/img/20210525165748.png)



LRU为了实现时间复杂度O(1)，采用HashMap+双向链表的数据结构，实get和put操作，如下图所示。

![LRU数据结构](https://raw.githubusercontent.com/Liu821218213/Typora-PicGo/master/img/20210525170310.png)

Java中可以采用LinkedHashMap源实现LRU，Python中可以调用OrderedDict()实现。



### 例题 [LeetCode146. LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)



### Java实现

下面用是用Java实现的几种方式，分别是

#### 调用Java的LinkedHashMap实现LRU

```java
package LRU;

import java.util.*;

/**
 * @author Orust
 * @create 2021/5/24 16:07
 */

// 146. LRU 缓存机制: https://leetcode-cn.com/problems/lru-cache/
// LinkedHashMap源码中写道: This kind of map is well-suited to building LRU caches.
// put和get的时间复杂度均为O(1)
// 空间复杂度为O(capacity) capacity为LRU缓存大小
// 广泛应用与OS内存页面调度，Redis缓存淘汰策略

// 样例输入
// ["LRUCache_4","put","put","put","get","put","put","get","put","put","get","put","get","get","get","put","put","get","put","get"]
// [[10],[7,28],[7,1],[8,15],[6],[10,27],[8,10],[8],[6,29],[1,9],[6],[10,7],[1],[2],[13],[8,30],[1,5],[1],[13,2],[12]]
// 预期结果
// [null,null,null,null,-1,null,null,10,null,null,29,null,9,-1,-1,null,null,5,null,-1]


// 调用Java的LinkedHashMap实现LRU

public class LRUCache {
    int capacity;
    LinkedHashMap<Integer, Integer> cache;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        cache = new LinkedHashMap<>();
    }

    public int get(int key) {
        if (cache.containsKey(key)) {
            int num = cache.remove(key);
            cache.put(key, num);
            return num;
        } else return -1;  // 未找到当前元素
    }

    public void put(int key, int value) {
        if (cache.containsKey(key)) {
            cache.remove(key);
        } else if (cache.size() == capacity) {
            // cache.remove(cache.entrySet().iterator().next().getKey());
            cache.remove(cache.keySet().iterator().next());
        }
        cache.put(key, value);
    }

    public static void main(String[] args) {
        LRUCache obj = new LRUCache(10);
        obj.put(7, 28);
        obj.put(7, 1);
        obj.put(8, 15);
        System.out.println(obj.get(6));
        obj.put(10, 27);
        obj.put(8, 10);
        System.out.println(obj.get(8));
        obj.put(6, 29);
        obj.put(1, 9);
        System.out.println(obj.get(6));
        obj.put(10, 7);
        System.out.println(obj.get(1));
        System.out.println(obj.get(2));
        System.out.println(obj.get(13));
        obj.put(8, 30);
        obj.put(1, 5);
        System.out.println(obj.get(1));
        obj.put(13, 2);
        System.out.println(obj.get(12));
    }
}
```

#### HashMap + 定义双向链表 实现LRU

```java
package LRU;

import java.util.*;

/**
 * @author Orust
 * @create 2021/5/24 16:18
 */

// HashMap + 定义双向链表 实现LRU

public class LRUCache_2 {
    int capacity;
    HashMap<Integer, Node> map;
    Node head;
    Node tail;

    private static class Node {
        int key;
        int value;
        Node prev;
        Node next;

        public Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    public LRUCache_2(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
    }

    public int get(int key) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            moveToHead(node);
            return node.value;
        } else return -1;
    }

    public void put(int key, int value) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            node.value = value;
            map.put(key, node);
            moveToHead(node);
        } else {
            if (map.size() == capacity)
                map.remove(popTail());
            Node node = new Node(key, value);
            map.put(key, node);
            addToHead(node);
        }
    }

    private void addToHead(Node node) {
        if (tail == null) {  // 当前链表为空
            head = node;
            tail = node;
        } else {
            head.prev = node;
            node.next = head;
            node.prev = null;
            head = node;
        }
    }

    private void moveToHead(Node node) {
        if (node == head) return;  // 判断是否是头尾节点并删除节点
        node.prev.next = node.next;
        if (node == tail) tail = tail.prev;
        else node.next.prev = node.prev;
        addToHead(node);
    }

    private int popTail() {
        if (tail == null) throw new NullPointerException("NullPointerException!");
        int key = tail.key;
        if (tail == head) {
            head = null;
            tail = null;
        } else {
            tail = tail.prev;
            tail.next = null;
        }
        return key;
    }

    public static void main(String[] args) {
        LRUCache_2 obj = new LRUCache_2(10);
        obj.put(7, 28);
        obj.put(7, 1);
        obj.put(8, 15);
        System.out.println(obj.get(6));
        obj.put(10, 27);
        obj.put(8, 10);
        System.out.println(obj.get(8));
        obj.put(6, 29);
        obj.put(1, 9);
        System.out.println(obj.get(6));
        obj.put(10, 7);
        System.out.println(obj.get(1));
        System.out.println(obj.get(2));
        System.out.println(obj.get(13));
        obj.put(8, 30);
        obj.put(1, 5);
        System.out.println(obj.get(1));
        obj.put(13, 2);
        System.out.println(obj.get(12));
    }
}

```

#### HashMap + 定义静态内部类 MyLinkedList　+　泛型 实现LRU

```java
package LRU;

import java.util.*;

/**
 * @author Orust
 * @create 2021/5/24 17:03
 */

// HashMap + 定义静态内部类MyLinkedList　+　泛型 实现LRU

public class LRUCache_3<K, V> {
    int capacity;
    Map<K, Node<K, V>> map = new HashMap<>();
    MyLinkedList<K, V> cache = new MyLinkedList<>();

    public LRUCache_3(int capacity) {
        this.capacity = capacity;
    }

    public V get(K key) {
        if (map.containsKey(key)) {
            Node<K, V> node = map.get(key);
            cache.moveToHead(node);
            return node.value;
        } else return null;
    }

    public void put(K key, V value) {
        if (map.containsKey(key)) {
            Node<K, V> node = map.get(key);
            node.value = value;
            map.put(key, node);
            cache.moveToHead(node);
        } else {
            if (map.size() == capacity)
                map.remove(cache.popTail());
            Node<K, V> node = new Node<>(key, value);
            map.put(key, node);
            cache.addToHead(node);
        }
    }

    private static class Node<K, V> {
        K key;
        V value;
        Node<K, V> prev;
        Node<K, V> next;

        Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }

    private static class MyLinkedList<K, V> {
        Node<K, V> head;  // head是最近使用的数据
        Node<K, V> tail;

        private void addToHead(Node<K, V> node) {
            if (tail == null) {  // 当前链表为空
                head = node;
                tail = node;
            } else {
                head.prev = node;
                node.next = head;
                node.prev = null;
                head = node;
            }
        }

        private void moveToHead(Node<K, V> node) {
            if (node == head) return;  // 判断是否是头尾节点并删除节点
            node.prev.next = node.next;
            if (node == tail) tail = tail.prev;
            else node.next.prev = node.prev;
            addToHead(node);
        }

        private K popTail() {
            if (tail == null) throw new NullPointerException("NullPointerException!");
            K key = tail.key;
            if (tail == head) {
                head = null;
                tail = null;
            } else {
                tail = tail.prev;
                tail.next = null;
            }
            return key;
        }
    }


    public static void main(String[] args) {
        LRUCache_3<String, Integer> obj = new LRUCache_3<>(10);
        obj.put("7", 28);
        obj.put("7", 1);
        obj.put("8", 15);
        System.out.println(obj.get("6"));
        obj.put("10", 27);
        obj.put("8", 10);
        System.out.println(obj.get("8"));
        obj.put("6", 29);
        obj.put("1", 9);
        System.out.println(obj.get("6"));
        obj.put("10", 7);
        System.out.println(obj.get("1"));
        System.out.println(obj.get("2"));
        System.out.println(obj.get("13"));
        obj.put("8", 30);
        obj.put("1", 5);
        System.out.println(obj.get("1"));
        obj.put("13", 2);
        System.out.println(obj.get("12"));
    }
}
```

#### 在tomcat6.x版本中被使用的LRU实现方式

```java
package LRU;

import java.util.*;

/**
 * @author Orust
 * @create 2021/5/25 10:53
 */

// 文章链接 https://www.iteye.com/blog/flychao88-1977653
// 在tomcat6.x版本中被使用的LRU实现方式

public class LRUCache_4 {
    /**
     * 链表节点
     *
     * @author Administrator
     */
    class CacheNode {
        CacheNode prev;//前一节点
        CacheNode next;//后一节点
        Object value;//值
        Object key;//键

        CacheNode() {
        }
    }

    public LRUCache_4(int i) {
        currentSize = 0;
        cacheSize = i;
        cache = new Hashtable(i);//缓存容器
    }

    /**
     * 获取缓存中对象
     *
     * @param key
     * @return
     */
    public Object get(Object key) {
        CacheNode node = (CacheNode) cache.get(key);
        if (node != null) {
            moveToHead(node);
            return node.value;
        } else {
            return null;
        }
    }

    /**
     * 添加缓存
     *
     * @param key
     * @param value
     */
    public void put(Object key, Object value) {
        CacheNode node = (CacheNode) cache.get(key);

        if (node == null) {
            //缓存容器是否已经超过大小.
            if (currentSize >= cacheSize) {
                if (tail != null)//将最少使用的删除
                    cache.remove(tail.key);
                removeLast();
            } else {
                currentSize++;
            }

            node = new CacheNode();
        }
        node.value = value;
        node.key = key;
        //将最新使用的节点放到链表头，表示最新使用的.
        moveToHead(node);
        cache.put(key, node);
    }

    /**
     * 将缓存删除
     *
     * @param key
     * @return
     */
    public Object remove(Object key) {
        CacheNode node = (CacheNode) cache.get(key);
        if (node != null) {
            if (node.prev != null) {
                node.prev.next = node.next;
            }
            if (node.next != null) {
                node.next.prev = node.prev;
            }
            if (tail == node)
                tail = node.prev;
            if (head == node)
                head = node.next;
        }
        return node;
    }

    public void clear() {
        head = null;
        tail = null;
    }

    /**
     * 删除链表尾部节点
     * 表示 删除最少使用的缓存对象
     */
    private void removeLast() {
        //链表尾不为空,则将链表尾指向null. 删除连表尾（删除最少使用的缓存对象）
        if (tail != null) {
            if (tail.prev != null)
                tail.prev.next = null;
            else
                head = null;
            tail = tail.prev;
        }
    }

    /**
     * 移动到链表头，表示这个节点是最新使用过的
     *
     * @param node
     */
    private void moveToHead(CacheNode node) {
        if (node == head)
            return;
        if (node.prev != null)
            node.prev.next = node.next;
        if (node.next != null)
            node.next.prev = node.prev;
        if (tail == node)
            tail = node.prev;
        if (head != null) {
            node.next = head;
            head.prev = node;
        }
        head = node;
        node.prev = null;
        if (tail == null)
            tail = head;
    }

    private int cacheSize;
    private Hashtable cache;//缓存容器
    private int currentSize;
    private CacheNode head;//链表头
    private CacheNode tail;//链表尾

    public static void main(String[] args) {
        LRUCache_4 obj = new LRUCache_4(10);
        obj.put(7, 28);
        obj.put(7, 1);
        obj.put(8, 15);
        System.out.println(obj.get(6));
        obj.put(10, 27);
        obj.put(8, 10);
        System.out.println(obj.get(8));
        obj.put(6, 29);
        obj.put(1, 9);
        System.out.println(obj.get(6));
        obj.put(10, 7);
        System.out.println(obj.get(1));
        System.out.println(obj.get(2));
        System.out.println(obj.get(13));
        obj.put(8, 30);
        obj.put(1, 5);
        System.out.println(obj.get(1));
        obj.put(13, 2);
        System.out.println(obj.get(12));
    }
}
```

### Python实现

#### Python调用 OrderedDict() 实现

```python
class LRUCache:

    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()


    def get(self, key: int) -> int:
        if key in self.cache:
            num = self.cache.pop(key)
            self.cache[key] = num
            return num
        return -1


    def put(self, key: int, value: int) -> None:
        if key not in self.cache:
            if len(self.cache) == self.capacity:
                self.cache.popitem(False)
        else:
            self.cache.pop(key)
        self.cache[key] = value
```



