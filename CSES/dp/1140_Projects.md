# [1140 — Projects](https://cses.fi/problemset/task/1140)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, sorting, binary search, weighted interval scheduling  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` projects you can attend. For each project, you know its starting and ending days and the amount of money you would get as reward. You can only attend one project during a day.

What is the maximum amount of money you can earn?

### Input

The first input line contains an integer `n`: the number of projects.

After this, there are `n` lines. Each such line has three integers `ai`, `bi`, and `pi`: the starting day, the ending day, and the reward.

### Output

Print one integer: the maximum amount of money you can earn.

### Constraints

- `1 <= n <= 2e5`
- `1 <= ai <= bi <= 1e9`
- `1 <= pi <= 1e9`

### Examples

**Input**
```
4
2 4 4
3 6 6
6 8 2
5 7 3
```

**Output**
```
7
```

---

## What this problem really is

Weighted interval scheduling: pick a subset of closed intervals `[ai, bi]` with pairwise **no day overlap**, maximize sum of rewards `pi`. `n` up to `2e5` → need `O(n log n)`.

## What to use

| Tool | Why |
|------|-----|
| Sort by end day | Process projects in order of finishing time |
| `dp[i]` | Best total reward using a subset of the first `i` projects (in that order) |
| Binary search | Find the latest previous project that ends before `ai` |
| `long long` | Rewards sum up to `2e5 * 1e9` |

## Approach

**Root idea:** after sorting by ending day, for each project decide take/skip. If you take project `i`, add `pi` to the best answer among projects that finish **before** day `ai` (no shared day — so previous end `<= ai - 1`).

**Mental picture:** timeline of jobs. Always consider jobs in the order they free the calendar. When a job ends, either ignore it or attach it to the best schedule that was free before it started.

**Data structures**

- Structs `{a, b, p}`, sort by `b` ascending.
- Array `dp[0..n]` (`dp[0] = 0`).
- Optionally store end days in a vector for `upper_bound`.

**Recurrence** (1-based projects after sort)

```
dp[i] = max(
  dp[i-1],                          // skip i
  p[i] + dp[j]                      // take i; j = count of projects with b[j] < a[i]
)
```

`j` = largest index with `b[j] < a[i]` (binary search on ends). If none, `j = 0`.

**Steps**

1. Read projects, sort by ending day.
2. For `i = 1 .. n`, binary-search compatible `j`, set `dp[i]` as above.
3. Answer `dp[n]`.

## Walkthrough (on a sample)

Projects: `[2,4]/4`, `[3,6]/6`, `[6,8]/2`, `[5,7]/3`.

Sorted by end: `(2,4,4)`, `(3,6,6)`, `(5,7,3)`, `(6,8,2)`.

- Take `(2,4,4)` then `(5,7,3)` → reward `7`, no overlap.
- Taking `(3,6,6)` alone is `6` (worse than `7`).

DP yields **`7`**.

## Pitfalls

- **Overlap rule** — same day conflicts; need previous `b < a`, not `b <= a` if both endpoints occupy the day (problem uses inclusive days).
- **`n = 2e5`** — `O(n^2)` “scan all previous” TLEs; must binary-search.
- Days up to `1e9` — sort/search on project indices/ends, don’t allocate by day.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Weighted intervals:** sort by end time; `dp[i] = max(skip, take + best before start)` with binary search for the predecessor.
