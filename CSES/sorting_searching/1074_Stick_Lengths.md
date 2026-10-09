# [1074 — Stick Lengths](https://cses.fi/problemset/task/1074)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** sorting, median, greedy  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` sticks with some lengths. Your task is to modify the sticks so that each stick has the same length.

You can either lengthen and shorten each stick. Both operations cost `x` where `x` is the difference between the new and original length.

What is the minimum total cost?

### Input

The first input line contains an integer `n`: the number of sticks.

Then there are `n` integers: `p1, p2, ..., pn`: the lengths of the sticks.

### Output

Print one integer: the minimum total cost.

### Constraints

- `1 <= n <= 2e5`
- `1 <= pi <= 1e9`

### Examples

**Input**
```
5
2 3 1 5 2
```

**Output**
```
5
```

---

## What this problem really is

Choose a target length `t`; cost is `sum |pi - t|`. Minimize over `t` (optimal `t` will be one of the `pi` values).

## What to use

| Tool | Why |
|------|-----|
| Median | For L1 (absolute deviation), the median minimizes the sum |
| Sort | Find median in `O(n log n)` |
| `long long` | Cost can be up to ~`n * 1e9` |

## Approach

**Root idea:** the sum of absolute deviations is minimized at a **median**. Make every stick equal to the median length; that is optimal.

**Mental picture:** a number line of stick lengths — the meeting point that minimizes total walking distance is the middle point of the sorted list.

**Data structures**

- Array `p` sorted ascending.
- Median: if `n` odd, `p[n/2]`; if even, any `t` between the two middle values works — pick `p[n/2]` (0-based) or `p[n/2-1]`.

**Steps**

1. Sort `p`.
2. `t = p[n/2]` (0-based middle).
3. `ans = sum |p[i] - t|` over all `i`.
4. Print `ans`.

## Walkthrough (on a sample)

Sorted: `1,2,2,3,5`. Median `2`.

Costs: `|1-2|+|2-2|+|2-2|+|3-2|+|5-2| = 1+0+0+1+3 = **5**`.

## Pitfalls

- Using the **mean** (optimal for squared error, not absolute).
- Picking min or max as target — usually much worse.
- `int` for the summed cost.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Equalize with min total `|diff|` cost → set everyone to the median**, then sum absolute deviations.
