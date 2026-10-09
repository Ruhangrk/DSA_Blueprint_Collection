# [1094 — Increasing Array](https://cses.fi/problemset/task/1094)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** greedy  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. You want to modify the array so that it is increasing, i.e., every element is at least as large as the previous element.

On each move, you may increase the value of any element by one. What is the minimum number of moves required?

### Input

The first input line contains an integer `n`: the size of the array.

Then, the second line contains `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print the minimum number of moves.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5
3 2 5 1 7
```

**Output**
```
5
```

---

## What this problem really is

Only **increases** allowed. Make the array non-decreasing with minimum total increments.

## What to use

| Tool | Why |
|------|-----|
| Greedy left-to-right | Never decrease; raise each dip up to the previous value |
| `long long` | Total moves can be large |

## Approach

**Root idea:** walk from left to right. If `x[i] < x[i-1]`, you must add `x[i-1] - x[i]` to position `i` (and treat the new value as the floor for the next step). Raising earlier elements more would only force later raises — not helpful.

**Mental picture:** a rising floor — whenever the next bar is shorter, pad it up to the floor height; the floor never drops.

**Steps**

1. `ans = 0`. For `i = 2..n` (1-based): if `x[i] < x[i-1]`, add `x[i-1]-x[i]` to `ans` and set `x[i] = x[i-1]`.
2. Print `ans`.

(You can avoid mutating by keeping `prev = max so far in the modified sense`.)

## Walkthrough (on a sample)

`3,2,5,1,7`:

- `2 → 3` (+1), array mentally `3,3,5,1,7`
- `1 → 5` (+4), total **5**
- `7` ok

## Pitfalls

- Decreasing elements (not allowed).
- Making the array strictly increasing (`>`) — `>=` is enough.
- `int` for the answer.

## Complexity

- Time: `O(n)`  
- Space: `O(1)` extra

## Key takeaway

**Min increments to non-decreasing = raise every dip up to the previous value** and sum those raises.
