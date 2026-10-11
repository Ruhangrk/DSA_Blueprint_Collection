# [1142 — Advertisement](https://cses.fi/problemset/task/1142)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Largest rectangle in histogram, monotonic stack  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A fence consists of `n` vertical boards. Each board has width 1, and the heights may differ. You want to attach a rectangular advertisement to the fence. What is the maximum area of such an advertisement?

### Input

- The first line contains an integer `n`: the width of the fence.
- The next line contains `n` integers `k[1], k[2], ..., k[n]`: the heights of the boards.

### Output

Print one integer: the maximum area of an advertisement.

### Constraints

- `1 <= n <= 2e5`
- `1 <= k[i] <= 1e9`

### Examples

**Input**
```
8
4 1 5 3 3 2 4 1
```

**Output**
```
10
```

---

## What this problem really is

Given a histogram of bar heights, find the largest axis-aligned rectangle that fits under it. Each rectangle is a contiguous run of boards, with height equal to the minimum board height in that run.

## What to use

| Tool | Why |
|------|-----|
| Monotonic stack (increasing heights) | Finds, for each board, how far it can extend left and right while staying the minimum |
| Start-index bookkeeping | Lets a popped bar report its full leftmost extent without a second pass |
| 64-bit integer | Area can reach `2e5 * 1e9 = 2e14` |

Trying all pairs `(l, r)` is `O(n^2)` and too slow for `n = 2e5`.

## Approach

**Root idea:** every optimal rectangle is limited by one board, its shortest one. For that board at position `i` with height `h`, the rectangle extends left and right until it hits a board shorter than `h`. So the best rectangle "owned" by board `i` has area `h * (right - left - 1)`, where `left` and `right` are the nearest strictly shorter boards.

**Mental picture:** picture the boards as a skyline of bars. A stack of bars that are still "open" is kept in increasing height from bottom to top. When a shorter bar arrives, every taller open bar is now closed: its rectangle cannot grow to the right anymore. Its width is known because we remember the leftmost position it could reach. Closing it gives one candidate area, and the bar that was popped passes its starting position on to the new, shorter bar, because the new bar can extend left at least that far.

**How that becomes the method**

1. Scan `i = 1..n`, with a sentinel height `0` at position `n + 1` so every bar is closed at the end.
2. Keep a stack of pairs `(start, height)` with strictly increasing heights.
3. For the current height `h` at position `i`: set `start = i`. While the stack top has height `>= h`, pop `(s, th)`: its rectangle spans `[s, i - 1]`, so area `= th * (i - s)`. Update the answer and set `start = s`.
4. Push `(start, h)`.
5. The answer is the maximum area seen.

**Data structures:**
- Stack of `(start, height)`: the bars that are still open, heights increasing from bottom to top. `start` is the leftmost position from which this bar's height is valid.
- `ans`: maximum area, `long long`.

## Walkthrough (on a sample)

Heights (1-indexed): `4 1 5 3 3 2 4 1`. Sentinel `0` at position 9.

- `i=1, h=4`: stack empty. Push `(1, 4)`. Stack: `[(1,4)]`.
- `i=2, h=1`: pop `(1,4)`: area `4 * (2-1) = 4`. `start = 1`. Push `(1, 1)`. Stack: `[(1,1)]`. `ans = 4`.
- `i=3, h=5`: top height 1 < 5, no pop. Push `(3, 5)`. Stack: `[(1,1),(3,5)]`.
- `i=4, h=3`: pop `(3,5)`: area `5 * (4-3) = 5`. `start = 3`. Top `(1,1)` has height 1 < 3, stop. Push `(3, 3)`. Stack: `[(1,1),(3,3)]`. `ans = 5`.
- `i=5, h=3`: top height 3 `>= 3`, pop `(3,3)`: area `3 * (5-3) = 6`. `start = 3`. Push `(3, 3)`. Stack: `[(1,1),(3,3)]`. `ans = 6`.
- `i=6, h=2`: pop `(3,3)`: area `3 * (6-3) = 9`. `start = 3`. Top `(1,1)` height 1 < 2, stop. Push `(3, 2)`. Stack: `[(1,1),(3,2)]`. `ans = 9`.
- `i=7, h=4`: top 2 < 4. Push `(7, 4)`. Stack: `[(1,1),(3,2),(7,4)]`.
- `i=8, h=1`: pop `(7,4)`: area `4 * (8-7) = 4`. `start = 7`. Pop `(3,2)`: area `2 * (8-3) = 10`. `start = 3`. Pop `(1,1)`: `1 * (8-1) = 7`. `start = 1`. Push `(1, 1)`. `ans = 10`.
- `i=9, h=0` (sentinel): pop `(1,1)`: `1 * (9-1) = 8`. Final `ans = 10`.

The winning rectangle is the `2` bar at position 6 extended over positions `3..7` (heights `5,3,3,2,4`), width 5, height 2, area 10.

## Pitfalls

- Using `>` instead of `>=` when popping. Equal heights should be merged so the `start` is carried forward correctly.
- Forgetting the final sentinel. Bars still on the stack at the end never get counted.
- Forgetting to carry `start` into the new push. Without it, a bar that could extend left is undercounted.
- Overflow: area can be `2e14`, so use `long long`.
- Off-by-one in width: the popped bar spans `[s, i-1]`, so width is `i - s`.

## Complexity

- Time: `O(n)`, each bar pushed and popped at most once.
- Space: `O(n)` for the stack.

## Key takeaway

For a histogram, every maximal rectangle is anchored at its shortest bar. A monotonic stack finds each bar's left and right extent in one pass.
