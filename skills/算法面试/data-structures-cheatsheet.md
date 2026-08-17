# Data Structures Cheatsheet — 数据结构速查表

> **When to Use / 使用场景**: 面试前快速复习数据结构的时间复杂度、使用场景和实现模板。

---

## Key Concepts / 核心概念总览

| 数据结构 | Access 访问 | Search 查找 | Insert 插入 | Delete 删除 | Space 空间 |
|---------|------------|------------|------------|------------|-----------|
| Array 数组 | O(1) | O(n) | O(n) | O(n) | O(n) |
| Linked List 链表 | O(n) | O(n) | O(1) | O(1) | O(n) |
| Stack 栈 | O(n) | O(n) | O(1) | O(1) | O(n) |
| Queue 队列 | O(n) | O(n) | O(1) | O(1) | O(n) |
| Hash Map 哈希表 | - | O(1) avg | O(1) avg | O(1) avg | O(n) |
| BST 二叉搜索树 | O(log n) avg | O(log n) avg | O(log n) avg | O(log n) avg | O(n) |
| Heap 堆 | - | O(n) | O(log n) | O(log n) | O(n) |
| Graph 图 | - | O(V+E) | O(1) | O(V+E) | O(V+E) |

---

## 1. Array 数组

### 特性
- 连续内存，随机访问 O(1)
- 插入/删除需要移动元素 O(n)
- 固定大小（静态数组）vs 动态扩展（动态数组）

### 适用场景
- 需要频繁随机访问
- 元素数量已知或变化不大
- 需要连续内存缓存友好

### Python 实现模板

```python
# 动态数组（Python list）
arr = [1, 2, 3, 4, 5]
arr.append(6)           # O(1) amortized
arr.insert(0, 0)        # O(n) - 头部插入需移动所有元素
arr.pop()               # O(1) - 尾部删除
arr.pop(0)              # O(n) - 头部删除需移动所有元素
arr[3]                   # O(1) - 随机访问

# 二维数组（矩阵）
matrix = [[0] * cols for _ in range(rows)]

# 数组切片
sub = arr[1:4]           # O(k) k为切片长度
```

### 常见陷阱
- Python list 的 `insert(0, x)` 是 O(n)，频繁头部操作用 `collections.deque`
- 二维数组初始化用 `[[0]*cols]*rows` 会导致浅拷贝问题

---

## 2. Linked List 链表

### 特性
- 非连续内存，通过指针连接
- 插入/删除 O(1)（已知位置时）
- 查找 O(n)，无随机访问

### 适用场景
- 频繁插入/删除（特别是头部）
- 不需要随机访问
- 实现其他数据结构（栈、队列）

### Python 实现模板

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

# 创建链表
def create_list(values):
    dummy = ListNode(0)
    curr = dummy
    for v in values:
        curr.next = ListNode(v)
        curr = curr.next
    return dummy.next

# 反转链表
def reverse_list(head):
    prev, curr = None, head
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt
    return prev

# 检测环
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False

# 找中点
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

---

## 3. Stack 栈

### 特性
- LIFO (Last In First Out) 后进先出
- push/pop/peek 都是 O(1)

### 适用场景
- 括号匹配
- 函数调用栈
- 表达式求值
- DFS 非递归实现
- 单调栈（下一个更大/更小元素）

### Python 实现模板

```python
# 使用 list 作为栈
stack = []
stack.append(1)    # push O(1)
stack.pop()         # pop  O(1)
stack[-1]           # peek O(1)

# 经典应用: 括号匹配
def is_valid(s):
    stack = []
    mapping = {')': '(', ']': '[', '}': '{'}
    for c in s:
        if c in mapping:
            if not stack or stack[-1] != mapping[c]:
                return False
            stack.pop()
        else:
            stack.append(c)
    return len(stack) == 0

# 单调栈: 下一个更大元素
def next_greater(nums):
    n = len(nums)
    result = [-1] * n
    stack = []
    for i in range(n):
        while stack and nums[i] > nums[stack[-1]]:
            result[stack.pop()] = nums[i]
        stack.append(i)
    return result
```

---

## 4. Queue 队列

### 特性
- FIFO (First In First Out) 先进先出
- enqueue/dequeue O(1)

### 适用场景
- BFS 广度优先搜索
- 任务调度
- 消息队列
- 滑动窗口

### Python 实现模板

```python
from collections import deque

# 使用 deque 作为队列
queue = deque()
queue.append(1)      # enqueue O(1)
queue.popleft()       # dequeue O(1)
queue[0]              # peek O(1)

# 双端队列
deque.appendleft(x)   # 左端插入 O(1)
deque.pop()            # 右端删除 O(1)

# 优先队列 (Min Heap)
import heapq
min_heap = []
heapq.heappush(min_heap, 3)
heapq.heappop(min_heap)    # 返回最小值

# Max Heap (取负)
max_heap = []
heapq.heappush(max_heap, -3)
-heapq.heappop(max_heap)   # 返回最大值
```

---

## 5. Hash Map 哈希表

### 特性
- 键值对存储，平均 O(1) 查找/插入/删除
- 最坏情况 O(n)（哈希冲突）
- Python: `dict`，Java: `HashMap`

### 适用场景
- 快速查找/去重
- 频率统计
- 缓存
- 两数之和等配对问题

### Python 实现模板

```python
# 基本操作
d = {}
d['key'] = 'value'       # 插入/更新 O(1)
val = d.get('key', None)  # 查找 O(1)
del d['key']               # 删除 O(1)

# 频率统计
from collections import Counter
freq = Counter("abracabra")  # {'a': 4, 'b': 2, 'r': 2, 'c': 1}

# defaultdict
from collections import defaultdict
groups = defaultdict(list)
for item in items:
    groups[key(item)].append(item)

# OrderedDict (Python 3.7+ dict 已有序)
from collections import OrderedDict
od = OrderedDict()
```

### 常见陷阱
- Python dict 在 3.7+ 保持插入顺序，但不要依赖此特性实现有序逻辑
- Java HashMap 的 `hashCode()` 和 `equals()` 必须一致

---

## 6. Tree 树

### Binary Tree 二叉树

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

# 前序遍历 (递归)
def preorder(root):
    if not root:
        return []
    return [root.val] + preorder(root.left) + preorder(root.right)

# 中序遍历 (递归)
def inorder(root):
    if not root:
        return []
    return inorder(root.left) + [root.val] + inorder(root.right)

# 层序遍历 (BFS)
def level_order(root):
    if not root:
        return []
    result, queue = [], deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result
```

### Binary Search Tree (BST) 二叉搜索树

```python
def search_bst(root, val):
    if not root or root.val == val:
        return root
    if val < root.val:
        return search_bst(root.left, val)
    return search_bst(root.right, val)

def insert_bst(root, val):
    if not root:
        return TreeNode(val)
    if val < root.val:
        root.left = insert_bst(root.left, val)
    else:
        root.right = insert_bst(root.right, val)
    return root
```

### 时间复杂度
| 操作 | Average 平均 | Worst 最坏 |
|------|-------------|-----------|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |

> **注意**: 最坏情况 O(n) 出现在树退化为链表时，使用 AVL 或红黑树保证 O(log n)。

---

## 7. Graph 图

### 表示方式

```python
# 邻接表 (推荐)
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

# 邻接矩阵
matrix = [
    [0, 1, 1, 0],  # A
    [1, 0, 0, 1],  # B
    [1, 0, 0, 1],  # C
    [0, 1, 1, 0],  # D
]
```

### BFS

```python
def bfs(graph, start):
    visited = set([start])
    queue = deque([start])
    result = []
    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return result
```

### DFS

```python
def dfs(graph, node, visited=None):
    if visited is None:
        visited = set()
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
    return visited
```

---

## 8. Heap 堆

### 特性
- 完全二叉树
- Min Heap: 父节点 ≤ 子节点
- Max Heap: 父节点 ≥ 子节点
- 插入/删除 O(log n)，查看极值 O(1)

### 适用场景
- Top K 问题
- 合并 K 个有序数组/链表
- 动态中位数
- 任务调度

### Python 实现

```python
import heapq

# Min Heap
heap = []
heapq.heappush(heap, 3)   # O(log n)
heapq.heappush(heap, 1)
heapq.heappush(heap, 2)
smallest = heap[0]          # O(1) peek
removed = heapq.heappop(heap)  # O(log n)

# 从列表建堆 — O(n)
nums = [3, 1, 4, 1, 5, 9]
heapq.heapify(nums)

# Top K 问题
def top_k(nums, k):
    return heapq.nlargest(k, nums)  # O(n log k)

# Max Heap
max_heap = [-x for x in nums]
heapq.heapify(max_heap)
max_val = -heapq.heappop(max_heap)
```

---

## 选择数据结构的决策树

```
需要什么操作？
│
├─ 随机访问 + 快速？
│   → Array / ArrayList
│
├─ 频繁插入/删除（已知位置）？
│   → Linked List
│
├─ LIFO（后进先出）？
│   → Stack
│
├─ FIFO（先进先出）？
│   → Queue / Deque
│
├─ 快速查找（key → value）？
│   → Hash Map / Dictionary
│
├─ 有序数据 + 快速查找？
│   → BST / TreeMap / SortedSet
│
├─ 维护极值（最大/最小）？
│   → Heap / Priority Queue
│
├─ 层级关系？
│   → Tree
│
└─ 多对多关系？
    → Graph
```

---

## Common Mistakes / 常见错误

1. **混淆 stack.append vs stack.push**: Python 用 `append` 代替 `push`
2. **用 list 做 queue**: `list.pop(0)` 是 O(n)，应用 `collections.deque`
3. **忘记建堆的时间复杂度**: `heapq.heapify` 是 O(n) 不是 O(n log n)
4. **BST 操作忘记处理空节点**: 递归终止条件要检查 `None`
5. **Graph 忘记 visited 集合**: 不加 visited 会导致无限循环
6. **Hash Map 的 key 不可变**: Python 用 tuple 不用 list 作为 key

---

## Pro Tips / 高手技巧

- **面试中先画图**: 用具体例子画出数据结构，让抽象变具体
- **选对数据结构比写对算法更重要**: 数据结构选对了，算法往往自然而然
- **考虑空间换时间**: Hash Map 是面试中最常用的空间换时间工具
- **Python 优先使用内置库**: `collections.deque`, `heapq`, `collections.Counter`
- **实现类时先写接口**: 先确定有哪些操作，再选数据结构

---

## Practice Questions / 推荐练习

| 数据结构 | 推荐题目 | LeetCode # |
|---------|---------|-----------|
| Array | Two Sum, Merge Intervals | 1, 56 |
| Linked List | Reverse, Merge, Cycle | 206, 21, 141 |
| Stack | Valid Parentheses, Min Stack | 20, 155 |
| Queue | Sliding Window Max, Moving Average | 239, 346 |
| Hash Map | Group Anagrams, LRU Cache | 49, 146 |
| Tree | Validate BST, Serialize/Deserialize | 98, 297 |
| Graph | Number of Islands, Clone Graph | 200, 133 |
| Heap | Top K Frequent, Find Median | 347, 295 |

---

> **最后提醒**: 面试时一定要说出你选择该数据结构的原因，以及权衡了哪些 trade-off。这比正确使用数据结构更能展示你的工程素养。
>
> In interviews, always explain WHY you chose a data structure and what trade-offs you considered. This demonstrates engineering maturity beyond just knowing the syntax.
