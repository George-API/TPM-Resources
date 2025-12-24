"""
===========================================================
Python DSA Quick Reference (Intermediate) — Syntax + Patterns
===========================================================

Goal:
- High-signal templates you can copy/paste into problems.
- Focus: intermediate patterns (windowing, stacks, heaps, graphs, DSU, DP).
- Comments explain "WHEN to use" + "WHAT invariant to keep".

Tip:
- Most DSA mistakes are not syntax; they're invariants:
  - What does the window represent?
  - What does the stack maintain?
  - What does the heap represent?
  - What does dp[i] mean?
"""

# ----------------------------
# Imports you actually use
# ----------------------------
from collections import defaultdict, Counter, deque
from functools import lru_cache
import heapq
import bisect
from itertools import accumulate


# ===========================================================
# 0) Complexity Cheatsheet (common ops)
# ===========================================================
"""
List:
- append/pop end: O(1) amortized
- insert/delete middle: O(n)
- membership (x in lst): O(n)

Dict / Set:
- add/get/remove/membership: O(1) average (hash table)

Deque:
- append/appendleft/pop/popleft: O(1)

Heap (heapq min-heap):
- push/pop: O(log n), peek: O(1)

Sort:
- Timsort: O(n log n) worst-case; very fast on partially-sorted data
"""


# ===========================================================
# 1) "DSA Python" syntax patterns (dicts/sets/sort/bisect/heap)
# ===========================================================

# ----------------------------
# Set: membership + uniqueness
# WHEN: detect repeats, visited, "have we seen this state?"
# ----------------------------
seen = set()
# seen.add(x)
# if x in seen: ...

# ----------------------------
# Dict: frequency counting (manual)
# WHEN: count items without importing Counter
# ----------------------------
mp = {}
# mp[key] = mp.get(key, 0) + 1

# ----------------------------
# defaultdict: avoid "if key not in dict"
# WHEN: adjacency lists, grouping, collecting
# ----------------------------
dd_list = defaultdict(list)     # dd_list[k].append(v)
dd_int = defaultdict(int)       # dd_int[k] += 1

# ----------------------------
# Counter: frequency counting + top-k
# WHEN: easiest way to count items
# ----------------------------
# cnt = Counter(arr)
# cnt.most_common(k)

# ----------------------------
# Sorting with keys
# WHEN: greedy by primary/secondary criteria
# ----------------------------
# arr.sort()                                # in-place
# arr_sorted = sorted(arr, reverse=True)
# arr.sort(key=lambda x: (x[0], -x[1]))      # tuple sort key

# ----------------------------
# bisect: binary search insertion points in sorted list
# WHEN: "first >= x", "first > x", LIS, coordinate insertion
# ----------------------------
# i = bisect.bisect_left(a, x)   # first idx with a[idx] >= x
# j = bisect.bisect_right(a, x)  # first idx with a[idx] > x

# ----------------------------
# heapq: min-heap
# WHEN: top-k, streaming min/max, scheduling, best-first search
# ----------------------------
# h = []
# heapq.heappush(h, x)
# x = heapq.heappop(h)
# top = h[0] if h else None

# max-heap trick: store negatives
# heapq.heappush(h, -x)
# mx = -heapq.heappop(h)


# ===========================================================
# 2) Arrays / Strings — core patterns
# ===========================================================

# -----------------------------------------------------------
# A) Two Pointers (sorted array, pair sum, partition)
# WHEN:
# - array is sorted (or can be sorted) and you need pair/triple relations
# - shrink search space from both ends
# INVARIANT:
# - l and r bound the remaining candidate region
# -----------------------------------------------------------
def two_sum_sorted(a, target):
    a = sorted(a)  # if you need indices, sort pairs (value, idx)
    l, r = 0, len(a) - 1
    while l < r:
        s = a[l] + a[r]
        if s == target:
            return True
        if s < target:
            l += 1  # need bigger sum -> move left pointer right
        else:
            r -= 1  # need smaller sum -> move right pointer left
    return False


# -----------------------------------------------------------
# B) Sliding Window (variable size)
# WHEN:
# - contiguous subarray/substring problems with a constraint
#   e.g. "at most K distinct", "no repeats", "sum <= K" (if nonnegative)
# INVARIANT:
# - window [l..r] is ALWAYS valid after the while-loop
# -----------------------------------------------------------
def longest_subarray_at_most_k_distinct(a, k):
    freq = defaultdict(int)
    l = 0
    best = 0

    for r, x in enumerate(a):
        freq[x] += 1

        # shrink until constraint satisfied
        while len(freq) > k:
            left_val = a[l]
            freq[left_val] -= 1
            if freq[left_val] == 0:
                del freq[left_val]
            l += 1

        # now window is valid; measure it
        best = max(best, r - l + 1)

    return best


# -----------------------------------------------------------
# C) Prefix Sum + Hash Map (subarray sum = k)
# WHEN:
# - count subarrays with sum exactly k
# - works with negatives too (unlike two pointers on sums)
# INVARIANT:
# - mp[p] = how many times we've seen prefix sum p so far
# -----------------------------------------------------------
def count_subarrays_sum_k(a, k):
    mp = {0: 1}   # prefix sum 0 seen once (empty prefix)
    pref = 0
    ans = 0

    for x in a:
        pref += x
        ans += mp.get(pref - k, 0)  # subarrays ending here with sum k
        mp[pref] = mp.get(pref, 0) + 1

    return ans


# -----------------------------------------------------------
# D) Difference Array (range updates)
# WHEN:
# - many "add val to [l..r]" updates, then build final array once
# INVARIANT:
# - diff stores boundary deltas; prefix over diff reconstructs values
# -----------------------------------------------------------
def apply_range_additions(n, updates):
    """
    updates: list of (l, r, val) inclusive
    """
    diff = [0] * (n + 1)

    for l, r, val in updates:
        diff[l] += val
        if r + 1 < len(diff):
            diff[r + 1] -= val

    arr = []
    cur = 0
    for i in range(n):
        cur += diff[i]
        arr.append(cur)
    return arr


# ===========================================================
# 3) Stack Patterns
# ===========================================================

# -----------------------------------------------------------
# A) Monotonic Stack (Next Greater Element / Daily Temps)
# WHEN:
# - "next greater/smaller to the right/left"
# INVARIANT:
# - stack keeps indices with monotonic property on values
#   (here: decreasing stack; top is smallest among stacked?)
# -----------------------------------------------------------
def next_greater_index(a):
    """
    Returns res where res[i] is the index of the next element > a[i], else -1
    """
    st = []                 # stack of indices; a[st] is decreasing
    res = [-1] * len(a)

    for i, x in enumerate(a):
        # x resolves all smaller elements on stack
        while st and a[st[-1]] < x:
            j = st.pop()
            res[j] = i
        st.append(i)

    return res


# -----------------------------------------------------------
# B) Parentheses validation
# WHEN:
# - matching pairs, nested structure validation
# INVARIANT:
# - stack contains opening brackets not yet closed
# -----------------------------------------------------------
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


# ===========================================================
# 4) Heap Patterns
# ===========================================================

# -----------------------------------------------------------
# A) Top-K largest with a size-K min-heap
# WHEN:
# - you want k largest items, stream-friendly
# INVARIANT:
# - heap always holds current best k items (smallest at top)
# -----------------------------------------------------------
def top_k_largest(nums, k):
    h = []
    for x in nums:
        heapq.heappush(h, x)
        if len(h) > k:
            heapq.heappop(h)
    return h  # unordered; contains k largest


# -----------------------------------------------------------
# B) K-way merge (merge sorted lists)
# WHEN:
# - merge many sorted sources efficiently
# INVARIANT:
# - heap stores the current "frontier" element of each list
# -----------------------------------------------------------
def merge_k_sorted(lists):
    h = []
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(h, (lst[0], i, 0))  # (value, list_id, index_in_list)

    out = []
    while h:
        val, li, idx = heapq.heappop(h)
        out.append(val)
        nxt = idx + 1
        if nxt < len(lists[li]):
            heapq.heappush(h, (lists[li][nxt], li, nxt))
    return out


# ===========================================================
# 5) Binary Search Templates
# ===========================================================

# -----------------------------------------------------------
# A) Standard binary search (value exists in sorted array)
# WHEN:
# - classic search on sorted list
# -----------------------------------------------------------
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


# -----------------------------------------------------------
# B) Binary search on answer (monotonic predicate)
# WHEN:
# - you can check feasibility for a given "mid"
# - predicate ok(mid) is monotonic (T...T F...F) or (F...F T...T)
# INVARIANT:
# - you shrink [lo, hi] to the first True (or last True) boundary
# -----------------------------------------------------------
def first_true(low, high, ok):
    """
    Finds smallest x in [low, high] such that ok(x) is True.
    Assumes at least one True exists in range.
    """
    lo, hi = low, high
    while lo < hi:
        mid = (lo + hi) // 2
        if ok(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo


# ===========================================================
# 6) Graph Patterns (BFS / DFS)
# ===========================================================

# -----------------------------------------------------------
# A) BFS shortest path in unweighted graph
# WHEN:
# - shortest number of edges / moves
# INVARIANT:
# - first time you visit a node is the shortest distance
# -----------------------------------------------------------
def bfs_shortest_dist(g, start):
    """
    g: adjacency list dict {u: [v1, v2, ...]}
    returns dist dict
    """
    q = deque([start])
    dist = {start: 0}

    while q:
        u = q.popleft()
        for v in g.get(u, []):
            if v not in dist:
                dist[v] = dist[u] + 1
                q.append(v)

    return dist


# -----------------------------------------------------------
# B) DFS for connectivity / components
# WHEN:
# - connected components, cycle detection (with extra state), topological sorts
# INVARIANT:
# - "seen" ensures each node processed once
# -----------------------------------------------------------
def dfs_visit_all(g):
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


# ===========================================================
# 7) Union-Find (Disjoint Set Union)
# ===========================================================

# -----------------------------------------------------------
# DSU / Union-Find
# WHEN:
# - dynamic connectivity
# - Kruskal's MST
# - grouping by relations, cycle detection in undirected graph
# INVARIANT:
# - find(x) returns representative root of x's component
# - union merges components; with rank/size keeps near O(α(n))
# -----------------------------------------------------------
class DSU:
    def __init__(self, n):
        self.p = list(range(n))
        self.r = [0] * n  # rank (approx tree height)

    def find(self, x):
        # path compression: flatten tree for faster future finds
        while self.p[x] != x:
            self.p[x] = self.p[self.p[x]]
            x = self.p[x]
        return x

    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return False  # already connected; union not performed

        # union by rank: attach smaller-rank tree under larger-rank root
        if self.r[ra] < self.r[rb]:
            ra, rb = rb, ra

        self.p[rb] = ra
        if self.r[ra] == self.r[rb]:
            self.r[ra] += 1

        return True


# ===========================================================
# 8) Intervals (merge / sweep)
# ===========================================================

# -----------------------------------------------------------
# A) Merge overlapping intervals
# WHEN:
# - union of intervals, simplify schedule
# INVARIANT:
# - output stays merged and sorted
# -----------------------------------------------------------
def merge_intervals(intervals):
    if not intervals:
        return []

    intervals.sort()  # sorts by start then end
    out = [list(intervals[0])]

    for s, e in intervals[1:]:
        last_s, last_e = out[-1]
        if s > last_e:
            out.append([s, e])
        else:
            out[-1][1] = max(last_e, e)

    return out


# -----------------------------------------------------------
# B) Sweep line (max overlap)
# WHEN:
# - maximum concurrent intervals, "how many active at once"
# IMPORTANT:
# - choose inclusive/exclusive: [s, e) is usually simplest
# -----------------------------------------------------------
def max_overlap(intervals):
    events = []
    for s, e in intervals:
        events.append((s, +1))
        events.append((e, -1))  # for [s, e) (end not included)

    events.sort()
    cur = best = 0
    for _, d in events:
        cur += d
        best = max(best, cur)
    return best


# ===========================================================
# 9) DP Patterns (Intermediate)
# ===========================================================

# -----------------------------------------------------------
# A) "Take / Skip" DP (House Robber style)
# WHEN:
# - choose non-adjacent items for max value
# INVARIANT:
# - take = best if we take current item
# - skip = best if we skip current item
# -----------------------------------------------------------
def max_non_adjacent_sum(a):
    take = 0
    skip = 0
    for x in a:
        new_take = skip + x
        new_skip = max(skip, take)
        take, skip = new_take, new_skip
    return max(take, skip)


# -----------------------------------------------------------
# B) 0/1 Knapsack optimization (2D -> 1D)
# WHEN:
# - each item used at most once
# INVARIANT:
# - iterate capacity backwards to avoid reusing same item twice
# -----------------------------------------------------------
def knapsack_01(items, W):
    """
    items: list of (weight, value)
    """
    dp = [0] * (W + 1)
    for w, val in items:
        for cap in range(W, w - 1, -1):
            dp[cap] = max(dp[cap], dp[cap - w] + val)
    return dp[W]


# -----------------------------------------------------------
# C) Memoized recursion (Top-down DP)
# WHEN:
# - state transitions are clear, recursion simplifies logic
# NOTE:
# - ensure state space is not huge or you may TLE/MLE
# -----------------------------------------------------------
def example_memo_dp(nums):
    n = len(nums)

    @lru_cache(None)
    def f(i):
        if i >= n:
            return 0
        # either skip i or take it and jump
        return max(f(i + 1), nums[i] + f(i + 2))

    return f(0)


# ===========================================================
# 10) Greedy: common "proof-y" rules of thumb
# ===========================================================
"""
- Intervals:
  - Sort by end time to maximize number of non-overlapping intervals.
- Scheduling:
  - Sort tasks, maintain a heap of active tasks by "worst" candidate to drop.
- Always ask:
  - "What is the local choice, and why can't it block the global optimum?"
"""


# ===========================================================
# 11) Common Bug Traps (read this before submitting)
# ===========================================================
"""
- Off-by-one in intervals: decide [s,e) vs [s,e] and do events accordingly.
- Sliding window:
  - After while-loop ends, window MUST be valid (your invariant).
- Hash maps:
  - Delete keys when freq hits 0 if you rely on len(freq).
- Recursion depth:
  - Python recursion can crash on large graphs; prefer iterative BFS/DFS.
- Using list for seen:
  - membership is O(n); use set/dict for O(1) average.
"""
