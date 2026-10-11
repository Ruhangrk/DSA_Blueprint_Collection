> **Warning: this solution might be incorrect.** No verified method exists yet. The only idea below was checked and fails on a counterexample.

# [3401 — Stick Difference](https://cses.fi/problemset/task/3401)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Status:** Unsolved (no verified solution)  

---

## Problem Statement

You are given `n` sticks with lengths `a[1..n]`. For each `k = 1..m`, make exactly `k` cuts (each cut splits one stick into two positive integer parts, so the stick count rises by one per cut). Print the smallest possible value of (longest stick - shortest stick) after the cuts.

### Input

- The first line has `n` and `m`.
- The second line has `a[1..n]`.

### Output

Print `m` integers: the answer for `k = 1..m`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a[i] <= 1e9`

### Examples

**Input**
```
3 3
7 3 2
```

**Output**
```
2 1 2
```

---

## What I have (and why it is not enough)

**Idea tried:** repeatedly cut the longest stick into two halves, and print max - min after each cut.

**Why it is wrong:** it fixes the split of the longest stick to two parts. Consider sticks `[100, 1]` and `k = 2`:

- Halving gives `100 -> 50, 50`, then `50 -> 25, 25`. Pieces: `25, 25, 50, 1`. Difference `49`.
- Cutting 100 into three parts `33, 33, 34` gives pieces `33, 33, 34, 1`. Difference `33`.

So the greedy is not optimal. It matches the sample only by coincidence.

**Where a correct method would start:** for a stick of length `a` cut into `p` parts, the smallest piece is at most `floor(a/p)` and the largest is at least `ceil(a/p)`. So choosing the number of parts per stick is the real decision. A correct solution needs to choose these counts (summing to `n + k`) to minimise the spread. This step is not worked out here.

## Complexity

Not established for a correct method.

## Key takeaway

Cutting the longest stick in half is a tempting but wrong greedy. The number of parts per stick must be chosen together, not one halving at a time.
