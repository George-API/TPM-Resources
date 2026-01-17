# Python DSA Quick Reference — Syntax + Patterns

**Scope**: Python-specific syntax and implementation patterns for data structures and algorithms.

**Purpose**: Use this for Python implementation syntax. For conceptual understanding, see the general DSA reference.

## Table of Contents

- [1. Time & Space Complexity](#1-time--space-complexity)
- [2. Data Structures](#2-data-structures)
- [3. Algorithm Patterns](#3-algorithm-patterns)
- [4. Problem-Solving Strategies](#4-problem-solving-strategies)

---

## 1. Time & Space Complexity

### Common Operations Complexity

**List**:
- Access by index: O(1)
- Search: O(n)
- Append/pop end: O(1) amortized
- Insert/delete middle: O(n)
- Membership (`x in lst`): O(n)

**Dict / Set**:
- Insert/delete/lookup: O(1) average
- Membership (`x in d`): O(1) average

**Deque**:
- Append/appendleft/pop/popleft: O(1)

**Heap (heapq)**:
- Push/pop: O(log n)
- Peek (`h[0]`): O(1)

**Sort**:
- `sorted()` / `.sort()`: O(n log n)

---

## 2. Data Structures

### Linear Structures

**List**:
```python
arr = []
arr.append(x)        # O(1)
arr.pop()            # O(1)
arr.insert(i, x)     # O(n)
x in arr             # O(n)
```

**Deque** (double-ended queue):
```python
from collections import deque
dq = deque()
dq.append(x)         # O(1)
dq.appendleft(x)     # O(1)
dq.pop()             # O(1)
dq.popleft()         # O(1)
```

**Stack** (using list):
```python
stack = []
stack.append(x)      # push
stack.pop()          # pop
stack[-1]            # peek
```

### Hash-Based Structures

**Dict**:
```python
d = {}
d[key] = value                    # O(1)
d.get(key, default)               # O(1)
d[key] = d.get(key, 0) + 1        # frequency counting
```

**Set**:
```python
s = set()
s.add(x)              # O(1)
x in s                # O(1)
```

**defaultdict** (avoid key checks):
```python
from collections import defaultdict
dd_list = defaultdict(list)       # dd_list[k].append(v)
dd_int = defaultdict(int)          # dd_int[k] += 1
```

**Counter** (frequency counting):
```python
from collections import Counter
cnt = Counter(arr)
cnt.most_common(k)     # top-k most frequent
```

### Heap (Priority Queue)

**Min-heap**:
```python
import heapq
h = []
heapq.heappush(h, x)   # O(log n)
x = heapq.heappop(h)   # O(log n)
top = h[0]             # O(1) - peek without pop

# Max-heap trick: store negatives
heapq.heappush(h, -x)
mx = -heapq.heappop(h)
```

**Top-K pattern**:
```python
def top_k_largest(nums, k):
    h = []
    for x in nums:
        heapq.heappush(h, x)
        if len(h) > k:
            heapq.heappop(h)  # remove smallest
    return h
```

### Sorting

```python
arr.sort()                                    # in-place
arr_sorted = sorted(arr)                      # new list
arr.sort(reverse=True)                        # descending
arr.sort(key=lambda x: x[1])                  # by key
arr.sort(key=lambda x: (x[0], -x[1]))         # multiple keys
```

### Binary Search (bisect)

```python
import bisect
a = [1, 3, 3, 5, 7]

i = bisect.bisect_left(a, 3)   # first index where a[i] >= 3
j = bisect.bisect_right(a, 3)  # first index where a[i] > 3
bisect.insort_left(a, x)       # insert x maintaining sorted order
```

---

## 3. Algorithm Patterns

### Two Pointers

**Use when**: Sorted array, pair sum, palindrome checking

```python
def two_sum_sorted(a, target):
    a = sorted(a)  # if need indices, sort pairs: (value, idx)
    l, r = 0, len(a) - 1
    while l < r:
        s = a[l] + a[r]
        if s == target:
            return True
        if s < target:
            l += 1
        else:
            r -= 1
    return False
```

### Sliding Window

**Use when**: Subarray/substring with constraint (at most K distinct, no repeats)

```python
from collections import defaultdict

def longest_subarray_at_most_k_distinct(a, k):
    freq = defaultdict(int)
    l = 0
    best = 0
    
    for r, x in enumerate(a):
        freq[x] += 1
        
        # Shrink until constraint satisfied
        while len(freq) > k:
            freq[a[l]] -= 1
            if freq[a[l]] == 0:
                del freq[a[l]]
            l += 1
        
        best = max(best, r - l + 1)
    
    return best
```

### Prefix Sum + Hash Map

**Use when**: Count subarrays with sum = k (works with negatives)

```python
def count_subarrays_sum_k(a, k):
    mp = {0: 1}   # prefix sum 0 seen once
    pref = 0
    ans = 0
    
    for x in a:
        pref += x
        ans += mp.get(pref - k, 0)
        mp[pref] = mp.get(pref, 0) + 1
    
    return ans
```

### Binary Search

**Standard search**:
```python
def binary_search(a, x):
    lo, hi = 0, len(a) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if a[mid] == x:
            return mid
        if a[mid] < x:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
```

**Search on answer** (monotonic predicate):
```python
def first_true(low, high, ok):
    """Find smallest x where ok(x) is True"""
    lo, hi = low, high
    while lo < hi:
        mid = (lo + hi) // 2
        if ok(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
```

### Depth-First Search (DFS)

**Use when**: Tree/graph traversal, connected components, cycle detection

```python
def dfs_visit_all(g):
    """g: adjacency list dict {u: [v1, v2, ...]}"""
    seen = set()
    
    def dfs(u):
        seen.add(u)
        for v in g.get(u, []):
            if v not in seen:
                dfs(v)
    
    for u in g:
        if u not in seen:
            dfs(u)
    
    return seen
```

### Breadth-First Search (BFS)

**Use when**: Shortest path (unweighted), level-order traversal

```python
from collections import deque

def bfs_shortest_dist(g, start):
    """g: adjacency list dict {u: [v1, v2, ...]}"""
    q = deque([start])
    dist = {start: 0}
    
    while q:
        u = q.popleft()
        for v in g.get(u, []):
            if v not in dist:
                dist[v] = dist[u] + 1
                q.append(v)
    
    return dist
```

### Dynamic Programming

**Take/Skip pattern** (House Robber style):
```python
def max_non_adjacent_sum(a):
    take = 0
    skip = 0
    for x in a:
        new_take = skip + x
        new_skip = max(skip, take)
        take, skip = new_take, new_skip
    return max(take, skip)
```

**0/1 Knapsack** (1D optimization):
```python
def knapsack_01(items, W):
    """items: list of (weight, value)"""
    dp = [0] * (W + 1)
    for w, val in items:
        # Iterate backwards to avoid reusing item
        for cap in range(W, w - 1, -1):
            dp[cap] = max(dp[cap], dp[cap - w] + val)
    return dp[W]
```

**Memoized recursion**:
```python
from functools import lru_cache

@lru_cache(None)
def f(i):
    if i >= n:
        return 0
    return max(f(i + 1), nums[i] + f(i + 2))
```

### Union-Find (Disjoint Set)

**Use when**: Dynamic connectivity, cycle detection, Kruskal's MST

```python
class DSU:
    def __init__(self, n):
        self.p = list(range(n))  # parent
        self.r = [0] * n         # rank
    
    def find(self, x):
        # Path compression
        while self.p[x] != x:
            self.p[x] = self.p[self.p[x]]
            x = self.p[x]
        return x
    
    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return False
        
        # Union by rank
        if self.r[ra] < self.r[rb]:
            ra, rb = rb, ra
        self.p[rb] = ra
        if self.r[ra] == self.r[rb]:
            self.r[ra] += 1
        return True
```

### Stack Patterns

**Monotonic stack** (next greater element):
```python
def next_greater_index(a):
    st = []  # stack of indices
    res = [-1] * len(a)
    
    for i, x in enumerate(a):
        while st and a[st[-1]] < x:
            j = st.pop()
            res[j] = i
        st.append(i)
    
    return res
```

**Parentheses validation**:
```python
def is_valid_brackets(s):
    pairs = {')': '(', ']': '[', '}': '{'}
    st = []
    for ch in s:
        if ch in pairs:
            if not st or st[-1] != pairs[ch]:
                return False
            st.pop()
        else:
            st.append(ch)
    return not st
```

### Interval Patterns

**Merge intervals**:
```python
def merge_intervals(intervals):
    if not intervals:
        return []
    
    intervals.sort()
    out = [list(intervals[0])]
    
    for s, e in intervals[1:]:
        last_s, last_e = out[-1]
        if s > last_e:
            out.append([s, e])
        else:
            out[-1][1] = max(last_e, e)
    
    return out
```

**Max overlap** (sweep line):
```python
def max_overlap(intervals):
    events = []
    for s, e in intervals:
        events.append((s, +1))
        events.append((e, -1))  # [s, e) convention
    
    events.sort()
    cur = best = 0
    for _, d in events:
        cur += d
        best = max(best, cur)
    return best
```

---

## 4. Problem-Solving Strategies

### Common Patterns

**Need fast lookup?** → `dict` or `set`
**Need sorted data?** → `sorted()` or `bisect`
**Need top-K elements?** → `heapq`
**Working with subarray/substring?** → Sliding window
**Working with sorted array?** → Binary search
**Need to track connected components?** → Union-Find

### Common Pitfalls

- **Off-by-one errors**: Array indices, loop boundaries, interval endpoints
- **Sliding window**: After while-loop, window must be valid
- **Hash maps**: Delete keys when freq hits 0 if using `len(freq)` for counting
- **Recursion depth**: Python recursion can crash on large graphs; prefer iterative BFS/DFS
- **Using list for seen**: `x in list` is O(n); use `set` for O(1)
- **Knapsack**: Iterate capacity backwards to avoid reusing items

### Quick Decision Guide

| Problem Type | Python Pattern |
|-------------|---------------|
| Fast lookup | `dict`, `set` |
| Frequency counting | `Counter`, `defaultdict(int)` |
| Top-K | `heapq` (min-heap trick) |
| Sorted insertion | `bisect` |
| Subarray sum = k | Prefix sum + hash map |
| At most K distinct | Sliding window |
| Shortest path (unweighted) | BFS with `deque` |
| Connected components | DFS or Union-Find |
| Overlapping intervals | Sort + merge or sweep line |
| Non-adjacent selection | Take/Skip DP |
| 0/1 Knapsack | 1D DP (iterate backwards) |
