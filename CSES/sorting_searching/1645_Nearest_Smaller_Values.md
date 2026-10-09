# [1645 — Nearest Smaller Values](https://cses.fi/problemset/task/1645)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** stack, monotonic stack  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to find for each array position the nearest position to its left having a smaller value.

### Input

The first input line has an integer `n`: the size of the array.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

### Output

Print `n` integers: for each array position the nearest position with a smaller value. If there is no such position, print `0`.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
8
2 5 1 4 8 3 2 5
```

**Output**
```
0 1 0 3 4 3 3 7
```

---

## What this problem really is

For each index `i`, find the largest `j < i` with `x[j] < x[i]` (strictly smaller). If none, `0`. (1-based positions in the output.)

## What to use

| Tool | Why |
|------|-----|
| Monotonic increasing stack of indices | Candidates for “nearest smaller to the left” |
| Amortized `O(n)` pops | Each index pushed/popped at most once |
| Why not nested scan | `O(n^2)` TLE at `2e5` |

## Approach

**Root idea:** scan left to right. The stack keeps indices with **increasing** values. For position `i`, pop while the top’s value is `>= x[i]` — those can never be the nearest smaller for `i` or for anyone to the right (a later element would prefer `i` or something even smaller). The new top (if any) is the nearest smaller to the left.

**Mental picture:** a skyline of rising bars; when a shorter bar arrives, taller bars behind it get demolished as useless left-neighbors; the first survivor to the left is your answer.

**Data structures**

- Stack of indices (1-based), values increasing bottom → top.

**Steps**

1. For `i = 1..n`:
   - While stack not empty and `x[stack.top] >= x[i]`: pop.
   - If stack empty: answer `0`; else answer `stack.top`.
   - Push `i`.
2. Print the `n` answers.

## Walkthrough (on a sample)

`2 5 1 4 8 3 2 5`

- `2`: stack empty → **0**; stack `[1]`
- `5`: top `2 < 5` → **1**; stack `[1,2]`
- `1`: pop `5`, pop `2` → **0**; stack `[3]`
- `4`: top `1 < 4` → **3**; …
- Continues to `0 1 0 3 4 3 3 7`

## Pitfalls

- Using `>` instead of `>=` when popping — equal values are not “smaller”.
- Nearest smaller to the **right** — different problem (scan other direction).
- Storing values only on the stack without indices — need positions for output.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Nearest smaller to the left = monotonic increasing stack** — pop `>=` current, then top is the answer.
