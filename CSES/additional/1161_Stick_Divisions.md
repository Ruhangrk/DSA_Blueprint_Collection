# [1161 — Stick Divisions](https://cses.fi/problemset/task/1161)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Greedy, min-heap, Huffman-style merging  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have a stick of length `x`. You want to split it into `n` sticks with given lengths `d[1..n]`, whose total is `x`. Each move takes any stick and splits it into two sticks. The cost of a move is the length of the stick being split. Find the minimum total cost.

### Input

- The first line has `x` and `n`.
- The second line has `d[1..n]`.

### Output

Print one integer: the minimum total cost.

### Constraints

- `1 <= x <= 1e9`
- `1 <= n <= 2e5`
- `sum of d[i] = x`

### Examples

**Input**
```
8 3
2 3 3
```

**Output**
```
13
```

---

## What this problem really is

Think of the splitting process as a tree. The whole stick is the root. Each split creates two children, and the leaves are the final pieces. Splitting a stick of length `L` costs `L`, so every internal node adds its length to the total. The total cost is the sum of all internal node values.

Each final piece `d[i]` sits at some depth in this tree. A piece at depth `t` is included in `t` internal nodes, so it contributes `d[i] * t`. Minimising the total cost means minimising `sum of d[i] * depth[i]`. This is exactly the problem Huffman coding solves.

## What to use

| Tool | Why |
|------|-----|
| Min-heap of stick lengths | Always lets us get the two smallest sticks quickly |
| Merge two smallest | Merging is the reverse of splitting, and it builds the tree bottom-up |
| 64-bit integers | The total cost can exceed `1e9` |

## Approach

**Root idea:** the splitting tree can be built in reverse. Start with the `n` pieces as leaves. Repeatedly join the two shortest pieces into one stick. Each join corresponds to one split of the merged stick. The cost of the join equals the cost of that split. The shortest pieces should be joined first, so that they sit deepest in the tree, where they are counted the most times.

**Mental picture:** imagine small sticks as leaves on a tree. Long sticks should stay near the top, where they appear in few internal nodes. Small sticks should sink deep, but they are then only paid for at each internal node above them. Joining the two smallest first keeps the big sticks near the root.

**Method**

1. Put all `d[i]` into a min-heap.
2. While the heap has more than one stick:
   - Pop the two smallest, `a` and `b`.
   - Add `a + b` to the total cost.
   - Push `a + b` back into the heap.
3. Print the total cost.

## Walkthrough (on the sample)

`x = 8`, sticks `2 3 3`.

| Step | Heap before | Pop | Join | Cost added | Total | Heap after |
|------|-------------|-----|------|------------|-------|------------|
| 1 | {2, 3, 3} | 2, 3 | 5 | 5 | 5 | {3, 5} |
| 2 | {3, 5} | 3, 5 | 8 | 8 | 13 | {8} |

Heap has one stick (`8`), so stop. Total cost is `13`.

Matches the expected output. The order matches the explanation: first split `8` into `3` and `5` (cost 8), then `5` into `2` and `3` (cost 5), which is the same tree read in reverse.

## Pitfalls

- Using `int` for the costs. The total can reach about `2e5 * 1e9`, so use `long long`.
- Joining the two largest sticks, or joining in input order. The greedy must always take the two smallest.
- Adding the cost of the final merge (the whole stick) twice, or leaving it out. Every merge is one split, and the loop adds each merge exactly once.
- Joining in input order instead of the two smallest. The greedy depends on always taking the smallest pieces.
- Sorting once and never reinserting the merged value. The merged stick must go back into the heap, because it may be among the smallest later.

## Complexity

- Time: `O(n log n)` for the heap operations.
- Space: `O(n)`.

## Key takeaway

Merging the two smallest pieces at each step gives the minimum total split cost. This is the same greedy as Huffman coding: small items are paid for the most times, so they should be joined first.
