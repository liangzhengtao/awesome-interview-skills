# LeetCode Patterns — 刷题模式指南

> **When to Use / 使用场景**: 准备科技公司编码面试时，系统性地掌握高频算法模式，告别盲目刷题。

---

## Key Concepts / 核心概念

LeetCode 2000+ 题目看似浩如烟海，但 80% 的面试题可以归纳为 **15 个核心模式**。掌握模式比刷题数量更重要。

| # | Pattern 模式 | Frequency 出现频率 | Difficulty 难度 |
|---|-------------|-------------------|----------------|
| 1 | Two Pointers 双指针 | ★★★★★ | Easy-Medium |
| 2 | Sliding Window 滑动窗口 | ★★★★★ | Medium |
| 3 | BFS/DFS 广度/深度优先搜索 | ★★★★★ | Medium-Hard |
| 4 | Dynamic Programming 动态规划 | ★★★★★ | Medium-Hard |
| 5 | Binary Search 二分查找 | ★★★★☆ | Easy-Medium |
| 6 | Backtracking 回溯 | ★★★★☆ | Medium-Hard |
| 7 | Stack 栈 | ★★★★☆ | Easy-Medium |
| 8 | Linked List 链表 | ★★★★☆ | Easy-Medium |
| 9 | Tree 树 | ★★★★☆ | Medium |
| 10 | Graph 图 | ★★★☆☆ | Medium-Hard |
| 11 | Heap/Priority Queue 堆/优先队列 | ★★★☆☆ | Medium |
| 12 | Union Find 并查集 | ★★☆☆☆ | Medium |
| 13 | Trie 字典树 | ★★☆☆☆ | Medium |
| 14 | Monotonic Stack 单调栈 | ★★☆☆☆ | Medium |
| 15 | Topological Sort 拓扑排序 | ★★☆☆☆ | Medium |

---

## Step-by-Step Framework / 分步框架

### 问题识别流程图

```
拿到题目
    │
    ├─ 数组/字符串，需要找子数组/子串？
    │   ├─ 是否有固定窗口大小？ → Sliding Window
    │   └─ 是否需要两端收缩？ → Two Pointers
    │
    ├─ 需要搜索所有可能组合？
    │   ├─ 排列/组合/子集？ → Backtracking
    │   └─ 图/矩阵？ → BFS/DFS
    │
    ├─ 有最优子结构？求最大/最小值？
    │   └─ Dynamic Programming
    │
    ├─ 有序数组中查找？
    │   └─ Binary Search
    │
    ├─ 需要维护某种顺序？
    │   ├─ 先进后出？ → Stack
    │   ├─ 最大/最小值优先？ → Heap
    │   └─ 拓扑依赖？ → Topological Sort
    │
    └─ 不确定 → 先用暴力法，再优化
```

---

## Pattern 1: Two Pointers 双指针

### 识别信号
- 有序数组需要找两个数的和
- 需要去重或找配对
- 链表中找中点、环、交点

### 模板

```python
def two_pointer(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        current = arr[left] + arr[right]
        if current == target:
            return [left, right]
        elif current < target:
            left += 1
        else:
            right -= 1
    return [-1, -1]
```

### 经典题目
- Two Sum II (LeetCode 167)
- 3Sum (LeetCode 15)
- Container With Most Water (LeetCode 11)

---

## Pattern 2: Sliding Window 滑动窗口

### 识别信号
- "Find the longest/shortest subarray..."
- 连续子数组/子串问题
- 包含关键词 "substring", "subarray", "contiguous"

### 模板

```python
def sliding_window(s):
    window = {}  # 或用 Set
    left = 0
    result = 0

    for right in range(len(s)):
        # 扩展窗口
        c = s[right]
        window[c] = window.get(c, 0) + 1

        # 收缩条件
        while window_needs_shrink:
            d = s[left]
            window[d] -= 1
            left += 1

        result = max(result, right - left + 1)

    return result
```

### 经典题目
- Longest Substring Without Repeating Characters (LeetCode 3)
- Minimum Window Substring (LeetCode 76)
- Sliding Window Maximum (LeetCode 239)

---

## Pattern 3: BFS/DFS 广度/深度优先搜索

### BFS 模板

```python
from collections import deque

def bfs(root):
    if not root:
        return []
    queue = deque([root])
    result = []
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```

### DFS 模板

```python
def dfs(node, result):
    if not node:
        return
    # 前序位置
    result.append(node.val)
    dfs(node.left, result)
    dfs(node.right, result)
    # 后序位置
```

### 经典题目
- Number of Islands (LeetCode 200)
- Binary Tree Level Order Traversal (LeetCode 102)
- Word Ladder (LeetCode 127)

---

## Pattern 4: Dynamic Programming 动态规划

### 识别信号
- 求最大/最小值
- 判断是否可行
- 统计方案数
- 有重叠子问题和最优子结构

### 模板

```python
def dp(nums):
    n = len(nums)
    # 1. 定义状态: dp[i] 表示什么
    dp = [0] * n
    # 2. 初始化
    dp[0] = nums[0]
    # 3. 状态转移方程
    for i in range(1, n):
        dp[i] = max(dp[i-1] + nums[i], nums[i])
    # 4. 返回结果
    return max(dp)
```

### DP 分类
| 类型 | 状态维度 | 经典题目 |
|------|---------|---------|
| 线性 DP | 1D | Climbing Stairs, House Robber |
| 背包问题 | 2D | 0/1 Knapsack, Coin Change |
| 区间 DP | 2D | Palindrome Partitioning |
| 树形 DP | 树节点 | Binary Tree Max Path Sum |
| 状态压缩 | bitmask | Traveling Salesman |

### 经典题目
- Climbing Stairs (LeetCode 70)
- Longest Increasing Subsequence (LeetCode 300)
- Edit Distance (LeetCode 72)

---

## Pattern 5: Binary Search 二分查找

### 模板

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

# 变体: 查找左边界
def binary_search_left(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        mid = left + (right - left) // 2
        if arr[mid] < target:
            left = mid + 1
        else:
            right = mid
    return left
```

---

## Pattern 6: Backtracking 回溯

### 模板

```python
def backtrack(path, choices, result):
    if 满足结束条件:
        result.append(path[:])
        return
    for choice in choices:
        if not is_valid(choice):
            continue
        path.append(choice)          # 做选择
        backtrack(path, choices, result)  # 递归
        path.pop()                   # 撤销选择
```

### 经典题目
- Subsets (LeetCode 78)
- Permutations (LeetCode 46)
- N-Queens (LeetCode 51)

---

## Pattern 7: Stack 栈

### 识别信号
- 括号匹配
- 表达式求值
- 单调栈求下一个更大/更小元素

### 模板: 单调栈

```python
def next_greater_element(nums):
    n = len(nums)
    result = [-1] * n
    stack = []  # 存索引
    for i in range(n):
        while stack and nums[i] > nums[stack[-1]]:
            idx = stack.pop()
            result[idx] = nums[i]
        stack.append(i)
    return result
```

---

## Pattern 8: Linked List 链表

### 常用技巧
- **Dummy Head 哑节点**: 简化头节点操作
- **快慢指针**: 找中点、检测环
- **递归**: 反转链表

```python
# 快慢指针找中点
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow

# 反转链表
def reverse_list(head):
    prev, curr = None, head
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt
    return prev
```

---

## Pattern 9–15: 速查表

| 模式 | 核心思想 | 时间复杂度 |
|------|---------|-----------|
| Tree 树 | 递归 + 遍历顺序 | O(n) |
| Graph 图 | BFS/DFS + 邻接表 | O(V+E) |
| Heap 堆 | 完全二叉树 + 维护极值 | O(log n) 插入/删除 |
| Union Find | 路径压缩 + 按秩合并 | O(α(n)) 近 O(1) |
| Trie 字典树 | 前缀共享 + 字符路径 | O(m) 查找 |
| Monotonic Stack | 维护单调递增/递减序列 | O(n) |
| Topological Sort | 入度 + BFS/Kahn's | O(V+E) |

---

## Templates / 解题模板

### 面试口头表达模板

```
1. 理解题目 (30s)
   "Let me make sure I understand... The input is ..., and I need to find/return ..."

2. 确认约束 (30s)
   "Are there any constraints on...? Can the array be empty? Are there duplicates?"

3. 暴力法 (1 min)
   "The brute force approach would be... which is O(n²). Can we do better?"

4. 优化思路 (2 min)
   "I notice that... so I could use [pattern name] to reduce it to O(n)."

5. 代码实现 (10-15 min)
   "Let me implement this step by step..."

6. 验证 (2 min)
   "Let me trace through an example... and check edge cases..."
```

---

## Common Mistakes / 常见错误

1. **盲目刷题**: 按题号刷不如按模式刷
2. **只看不写**: 必须亲手实现，纸上手写练习
3. **忽略边界条件**: 空数组、单元素、全相同、整数溢出
4. **不解释思路**: 面试重思路沟通，不是比谁写得快
5. **过度优化**: 面试中先写暴力法再优化，不要跳过暴力法
6. **忽略时间复杂度**: 每次写完都分析 Big O
7. **死记硬背**: 理解原理比背模板更重要

---

## Pro Tips / 高手技巧

- **面试前 1 周**: 只刷 Medium，每题限时 20 分钟
- **面试前 1 天**: 复习模板，不做新题
- **面试中**: 先说思路，确认后再写代码
- **如果卡住了**: 举具体例子手动模拟，而不是干想
- **写完后**: 主动分析时间/空间复杂度，主动说边界情况
- **如果要求优化**: 回到问题识别流程图，尝试不同模式

---

## Practice Questions / 练习题清单

### 按模式推荐（每模式 3 题，共 45 题）

| 模式 | 题目 | LeetCode # |
|------|------|-----------|
| Two Pointers | Two Sum II, 3Sum, Trapping Rain Water | 167, 15, 42 |
| Sliding Window | Longest Substring, Min Window, Max Sliding Window | 3, 76, 239 |
| BFS/DFS | Islands, Level Order, Word Ladder | 200, 102, 127 |
| DP | Climbing Stairs, LIS, Edit Distance | 70, 300, 72 |
| Binary Search | Search in Rotated Array, Find Min, Median of Two | 33, 153, 4 |
| Backtracking | Subsets, Permutations, N-Queens | 78, 46, 51 |
| Stack | Valid Parentheses, Daily Temperatures, Largest Rectangle | 20, 739, 84 |
| Linked List | Reverse, Merge Two, Detect Cycle | 206, 21, 141 |
| Tree | Max Depth, Invert Tree, Validate BST | 104, 226, 98 |
| Graph | Clone Graph, Course Schedule, Alien Dictionary | 133, 207, 269 |
| Heap | Top K, Merge K Lists, Find Median | 347, 23, 295 |
| Union Find | Redundant Connection, Number of Provinces | 684, 547 |
| Trie | Implement Trie, Word Search II | 208, 212 |
| Monotonic Stack | Daily Temperatures, Next Greater Element | 739, 496 |
| Topological Sort | Course Schedule, Course Schedule II | 207, 210 |

---

> **记住**: 面试官看的不是你能不能做出来，而是你的思维过程。清晰地沟通你的想法，比写出完美代码更重要。
>
> The interviewer cares more about your thought process than the final code. Communicate clearly, think out loud, and show structured problem-solving skills.
