# Data Structures & Algorithms — Conceptual Overview

**Scope**: Platform-agnostic concepts, patterns, and complexity analysis for data structures and algorithms.

**Purpose**: Use this for understanding when to use which data structure/algorithm and why. For implementation syntax, see language-specific references.

## Table of Contents

- [1. Time & Space Complexity](#1-time--space-complexity)
- [2. Data Structures](#2-data-structures)
- [3. Algorithm Patterns](#3-algorithm-patterns)
- [4. Problem-Solving Strategies](#4-problem-solving-strategies)

---

## 1. Time & Space Complexity

### Big O Notation
- **O(1)** - Constant time: direct array access, hash table lookup
- **O(log n)** - Logarithmic: binary search, balanced tree operations
- **O(n)** - Linear: single pass through array, linked list traversal
- **O(n log n)** - Linearithmic: efficient sorting (merge sort, heap sort)
- **O(n²)** - Quadratic: nested loops, bubble sort
- **O(2ⁿ)** - Exponential: brute force recursion, subset generation
- **O(n!)** - Factorial: permutations

### Common Operations Complexity

**Array/List**:
- Access by index: O(1)
- Search: O(n)
- Insert/delete at end: O(1) amortized
- Insert/delete at middle: O(n)

**Hash Table/Dictionary**:
- Insert/delete/lookup: O(1) average, O(n) worst case
- Iteration: O(n)

**Binary Search Tree**:
- Insert/delete/search: O(log n) average, O(n) worst case (unbalanced)
- Traversal: O(n)

**Heap**:
- Insert/extract min: O(log n)
- Peek: O(1)

---

## 2. Data Structures

### Linear Structures

**Array/List**:
- **Use when**: Random access needed, fixed size known, cache-friendly operations
- **Trade-offs**: Fast access, but insert/delete in middle is expensive

**Linked List**:
- **Use when**: Frequent insertions/deletions, unknown size, no random access needed
- **Trade-offs**: O(1) insert/delete, but O(n) access/search

**Stack** (LIFO):
- **Use when**: Expression evaluation, backtracking, undo operations, parentheses matching
- **Operations**: Push, pop, peek (all O(1))

**Queue** (FIFO):
- **Use when**: BFS, task scheduling, buffering
- **Operations**: Enqueue, dequeue (all O(1))

**Deque** (Double-ended queue):
- **Use when**: Sliding window, palindrome checking
- **Operations**: Add/remove from both ends (all O(1))

### Hash-Based Structures

**Hash Table/Dictionary**:
- **Use when**: Fast lookups, key-value mapping, duplicate detection, frequency counting
- **Trade-offs**: O(1) average operations, but no ordering, hash collisions possible

**Set**:
- **Use when**: Membership testing, duplicate removal, union/intersection operations
- **Trade-offs**: O(1) membership test, but no ordering

### Tree Structures

**Binary Tree**:
- **Use when**: Hierarchical data, expression trees, decision trees
- **Traversals**: In-order (left-root-right), pre-order (root-left-right), post-order (left-right-root)

**Binary Search Tree (BST)**:
- **Use when**: Sorted data with dynamic insertions, range queries
- **Invariant**: Left child < root < right child
- **Trade-offs**: O(log n) operations if balanced, O(n) if unbalanced

**Heap** (Priority Queue):
- **Use when**: Top-K problems, scheduling, Dijkstra's algorithm
- **Min-heap**: Parent ≤ children
- **Max-heap**: Parent ≥ children

**Trie** (Prefix Tree):
- **Use when**: String prefix matching, autocomplete, word search
- **Trade-offs**: O(m) search where m is string length, but high space usage

### Graph Structures

**Adjacency List**:
- **Use when**: Sparse graphs, frequent edge queries
- **Space**: O(V + E)
- **Edge lookup**: O(degree)

**Adjacency Matrix**:
- **Use when**: Dense graphs, frequent edge existence checks
- **Space**: O(V²)
- **Edge lookup**: O(1)

---

## 3. Algorithm Patterns

### Two Pointers
- **Use when**: Sorted array problems, palindrome checking, pair sum
- **Pattern**: One pointer at start, one at end, move based on condition
- **Complexity**: O(n) time, O(1) space

### Sliding Window
- **Use when**: Subarray/substring problems with fixed or variable size
- **Pattern**: Maintain window boundaries, expand/contract based on condition
- **Complexity**: O(n) time, O(1) or O(k) space where k is window size

### Binary Search
- **Use when**: Sorted data, search space reduction, optimization problems
- **Pattern**: Eliminate half of search space each iteration
- **Complexity**: O(log n) time, O(1) space

### Depth-First Search (DFS)
- **Use when**: Tree/graph traversal, path finding, backtracking, cycle detection
- **Pattern**: Explore as far as possible before backtracking
- **Complexity**: O(V + E) time, O(V) space (recursion stack)

### Breadth-First Search (BFS)
- **Use when**: Shortest path (unweighted), level-order traversal, social network analysis
- **Pattern**: Explore level by level using queue
- **Complexity**: O(V + E) time, O(V) space (queue)

### Dynamic Programming
- **Use when**: Overlapping subproblems, optimal substructure
- **Pattern**: Store results of subproblems, build up solution
- **Approaches**: Top-down (memoization) or bottom-up (tabulation)
- **Complexity**: Typically O(n) or O(n²) time, O(n) or O(n²) space

### Greedy Algorithms
- **Use when**: Local optimal choice leads to global optimum
- **Pattern**: Make locally optimal choice at each step
- **Examples**: Activity selection, fractional knapsack, Huffman coding
- **Note**: Requires proof that greedy choice is optimal

### Union-Find (Disjoint Set)
- **Use when**: Connectivity problems, cycle detection in undirected graphs, Kruskal's algorithm
- **Operations**: Union (connect components), Find (find root)
- **Complexity**: Nearly O(1) with path compression and union by rank

### Topological Sort
- **Use when**: Dependency resolution, course prerequisites, build systems
- **Pattern**: Order nodes such that all dependencies come before dependents
- **Complexity**: O(V + E) time, O(V) space

---

## 4. Problem-Solving Strategies

### Problem Analysis
1. **Understand constraints**: Input size, value ranges, time/space limits
2. **Identify patterns**: Recognize common problem types (array, string, tree, graph)
3. **Consider edge cases**: Empty input, single element, duplicates, null values

### Approach Selection
1. **Brute force first**: Understand the problem, then optimize
2. **Look for invariants**: What property must be maintained?
3. **Consider data structures**: Which structure fits the operations needed?
4. **Think about complexity**: Can we do better than O(n²)?

### Optimization Techniques
- **Sorting**: Often enables O(n log n) solutions instead of O(n²)
- **Hashing**: Trade space for O(1) lookups instead of O(n) search
- **Two pointers**: Reduce nested loops to single pass
- **Sliding window**: Avoid recomputing overlapping subproblems
- **Memoization**: Cache results of expensive recursive calls
- **Precomputation**: Calculate once, use many times

### Common Pitfalls
- **Off-by-one errors**: Array indices, loop boundaries
- **Integer overflow**: Large numbers, multiplication
- **Null/empty handling**: Edge cases, empty collections
- **State management**: Forgetting to update variables in loops
- **Base cases**: Missing termination conditions in recursion

### Testing Strategy
- **Unit tests**: Small inputs, edge cases
- **Complexity verification**: Check time/space usage on large inputs
- **Correctness**: Verify with known examples, mathematical proofs where possible

---

## Quick Decision Guide

**Need fast lookup?** → Hash table
**Need sorted data?** → Binary search tree or sorted array
**Need top-K elements?** → Heap
**Need to traverse tree?** → DFS (recursive) or BFS (iterative)
**Need shortest path?** → BFS (unweighted) or Dijkstra (weighted)
**Need to find cycles?** → DFS with visited tracking
**Need to optimize recursive solution?** → Dynamic programming
**Working with sorted array?** → Consider binary search
**Working with subarray/substring?** → Consider sliding window
**Need to track connected components?** → Union-Find

