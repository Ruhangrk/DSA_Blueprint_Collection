# [3314 — Mountain Range](https://cses.fi/problemset/task/3314)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, monotonic stack, segment tree  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` mountains in a row, each with a specific height. You begin your hang gliding route from some mountain.

You can glide from mountain `a` to mountain `b` if mountain `a` is taller than mountain `b` and all mountains between `a` and `b`.

What is the maximum number of mountains you can visit on your route?

### Input

The first line has an integer `n`: the number of mountains.

The next line has `n` integers `h1, h2, ..., hn`: the heights of the mountains.

### Output

Print one integer: the maximum number of mountains.

### Constraints

- `1 <= n <= 2e5`
- `1 <= hi <= 1e9`

### Examples

**Input**
```
10
20 15 17 35 25 40 12 19 13 12
```

**Output**
```
5
```

---

## What this problem really is

Directed jumps on an array: edge `a -> b` exists iff `h[a]` is **strictly greater** than every height on the segment between `a` and `b` (including `h[b]`). Find the longest path (max number of vertices) in this DAG. Start anywhere.

## What to use

| Tool | Why |
|------|-----|
| `dp[i]` | Longest route **starting at** mountain `i` (count of mountains) |
| Nearest greater (stack) | From `i`, one-glide targets lie strictly between `i` and its nearest taller mountains on each side |
| Process short → tall | When updating `i`, all possible next mountains (shorter) already have final `dp` |
| Segment tree (range max) | Query `max dp` over that open interval in `O(log n)` |

## Approach

**Root idea:** a glide from `i` can land on any mountain `j` in the open interval up to the nearest **strictly taller** mountain on that side — because that is exactly when `h[i]` dominates the range. After landing on `j`, continue optimally. So

```
dp[i] = 1 + max(dp[j] for valid next j), or 1 if none
```

Answer = `max dp[i]`.

**Mental picture:** from peak `i`, you may dive into the valley on your left until you hit a taller wall, and same on the right. Each dive picks one mountain there; then that mountain becomes the new start.

**Data structures**

- `h[0..n-1]`, `dp[0..n-1]`.
- Monotonic stack → `left[i]`, `right[i]` = nearest index with height `>` `h[i]` (or `-1` / `n` if none). Valid next indices for `i` are in `(left[i], i)` and `(i, right[i])`.
- Segment tree storing `dp` values for range-max queries.
- Order mountains by increasing height (tie-break by index).

**Steps**

1. Compute `left[i]`, `right[i]` with a mono stack.
2. Sort indices by `h[i]` ascending.
3. For each `i` in that order:
   - Query `M = max dp` on `(left[i]+1 .. i-1)` and `(i+1 .. right[i]-1)` (empty → `0`).
   - Set `dp[i] = 1 + M`.
   - Point-update segment tree at `i` with `dp[i]`.
4. Print the maximum `dp[i]`.

Processing short→tall is valid because every legal next mountain is strictly shorter, so its `dp` is already fixed.

## Walkthrough (on a sample)

Heights: `20 15 17 35 25 40 12 19 13 12`.

Short mountains near the end (the `12`s, `13`, `19`, …) get small `dp` first. Larger peaks see wide valleys and pick the best `dp` inside. The best route visits **5** mountains (sample output).

You do not need to list the path for the judge — only the length.

## Pitfalls

- **`n = 2e5`** — cannot build an explicit dense jump graph or run `O(n^2)` DP.
- **Nearest greater must be strict `>`** — equal heights block domination.
- **Empty valley** → `dp[i] = 1` (visit only yourself).
- Update the segment tree **after** computing `dp[i]`, not before the query.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Dominating jumps = nearest-greater windows.** Longest dive path = DP by increasing height, with a range-max structure over already-computed shorter mountains.
