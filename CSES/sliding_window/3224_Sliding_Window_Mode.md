# [3224 — Sliding Window Mode](https://cses.fi/problemset/task/3224)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, frequency, policy / set of pairs  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the mode of each window of `k` elements, from left to right.

The mode is the most frequent element in an array. If there are several possible modes, choose the smallest of them.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

Then there are `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print `n-k+1` values: the modes.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
8 3
1 2 3 2 5 2 4 4
```

**Output**
```
1 2 2 2 2 4
```

---

## What this problem really is

For every window, output the value with maximum frequency; ties → smallest value.

## What to use

| Tool | Why |
|------|-----|
| `freq[value]` map | Current counts |
| Ordered set of `(freq, -value)` or `(freq, value)` | Best mode = max freq, then min value |
| Lazy delete / erase-reinsert | On add/remove, update the ordered set |

## Approach

**Root idea:** maintain frequencies and a balanced BST keyed by `(-count, value)` (or `(count, value)` with reverse compare) so `*begin` / `rbegin` is always the mode.

**Mental picture:** a leaderboard of numbers ranked by how often they appear in the window; frequency bumps reshuffle the board; the top name (with the tie-break) is the mode.

**How that becomes the method**

1. `unordered_map` or compressed `freq`.
2. `set<pair<int,int>>` storing `{count, value}` with custom order: larger count first, then smaller value.
3. `add(v)`: erase old `{freq[v], v}` if any; `freq[v]++`; insert new.
4. `rem(v)`: erase; `freq[v]--`; insert if `>0`.
5. Slide window; each time print the best pair’s value.

**Data structures:** hash map + `set` of pairs; or PBDS / two multisets.

## Walkthrough (on a sample)

Window `[1,2,3]`: all freq 1 → smallest `1`. `[2,3,2]`: `2` wins. Later `[2,4,4]`: `4` wins.

## Pitfalls

- Tie-break wrong way (largest instead of smallest).  
- Forgetting to erase the old pair before changing freq.  
- `O(nk)` recount.

## Complexity

- Time: `O(n log k)`  
- Space: `O(k)`

## Key takeaway

**Sliding mode = freq map + ordered set keyed by (count, value)** with the problem’s tie-break.
