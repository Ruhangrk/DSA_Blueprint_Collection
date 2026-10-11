# [3301 — Maximum Average Subarrays](https://cses.fi/problemset/task/3301)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Prefix sums, convex hull, slope queries, monotone structure  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. For each `i = 1, 2, ..., n`, find the subarray that ends at index `i` and has the largest average. If several subarrays tie for the largest average, choose the longest one. Output its length.

### Input

- The first line has `n`.
- The next line has `x[1], ..., x[n]`.

### Output

Print `n` integers: the length of the best subarray ending at each index.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x[i] <= 1e6`

### Examples

**Input**
```
7
1 6 4 6 2 5 5
```

**Output**
```
1 1 2 1 4 1 2
```

**Explanation:** For `i = 5`, the best subarray is `[6, 4, 6, 2]` with average `4.5`, length 4.

---

## What this problem really is

Turn the array into a path of prefix sums. The average of a subarray `x[j+1..i]` is the slope between the points `(j, S[j])` and `(i, S[i])`, where `S` is the prefix sum. So for each `i`, we want the earlier point with the steepest slope to `(i, S[i])`.

## What to use

| Tool | Why |
|------|-----|
| Prefix sums `S[0..n]` | Subarray sums become differences |
| Lower convex hull of earlier points | The steepest slope into a new point always comes from the hull |
| Binary search on the hull | Finds the best hull point in `O(log n)` |
| Tie-break by smallest index | Ties must give the longest subarray |

## Approach

**Root idea:** the average of `x[j+1..i]` equals `(S[i] - S[j]) / (i - j)`, which is the slope between two points. Maximizing the average over `j < i` means finding the point `j` such that the line from it to `(i, S[i])` is as steep as possible.

**Mental picture:** plot the points `(0, S[0]), (1, S[1]), ...`. Stand at the newest point `i` and rotate a ruler around it. The ruler is steepest when it just touches the bottom of the earlier points. The points it touches form the lower convex hull, which is the "floor" of the earlier points. So only hull points can be the answer. The first hull point the ruler touches from the left side is the one to pick, and when points are on one straight line, pick the leftmost (longest subarray).

**How that becomes the method**

1. Build prefix sums `S[0] = 0`, `S[k] = x[1] + ... + x[k]`.
2. Keep a stack that holds the lower convex hull of points `0..i-1`. Points are added in increasing `x` order, so the hull is a stack.
3. For each `i` from 1 to `n`:
   - Binary search the hull for the vertex `j` that maximizes `(S[i] - S[j]) / (i - j)`. Move right along the hull while the next vertex gives a strictly higher average. Compare averages with cross multiplication, not floating point.
   - Answer for `i` is `i - j`.
   - Add point `i` to the hull: pop the top while the last two hull points and the new point do not turn strictly counter-clockwise (pop when the turn is straight or clockwise), then push.
4. Output the answers in order.

**Data structures:**
- `S[0..n]`: prefix sums, `long long`.
- `hull`: a stack of indices with increasing `x`, forming the lower convex hull.

## Walkthrough (on a sample)

`x = 1 6 4 6 2 5 5`. Prefix sums `S = [0, 1, 7, 11, 17, 19, 24, 29]`.

Start with hull `[0]`.

- `i = 1`: hull `[0]`. Only candidate `j = 0`, average `(1-0)/1 = 1`. Answer `1`. Add `1`: hull `[0, 1]`.
- `i = 2` (`S = 7`): candidates `j = 0`: `7/2 = 3.5`; `j = 1`: `6/1 = 6`. Best `j = 1`, answer `1`. Add `2`: slope `1 -> 2` is `6`, slope `0 -> 1` is `1`. Lower hull keeps increasing slopes, so hull `[0, 1, 2]`.
- `i = 3` (`S = 11`): candidates `j = 0`: `11/3 = 3.67`; `j = 1`: `10/2 = 5`; `j = 2`: `4/1 = 4`. Best `j = 1`, answer `2`. Add `3`: point `2` (at `(2,7)`) lies above the line from `1` to `3` (slope `(11-1)/2 = 5`, the line at `x = 2` is `6`, and `7 > 6`), so pop `2`. Hull `[0, 1, 3]`.
- `i = 4` (`S = 17`): candidates `j = 0`: `4.25`; `j = 1`: `16/3 = 5.33`; `j = 3`: `6/1 = 6`. Best `j = 3`, answer `1`. Add `4`, hull `[0, 1, 3, 4]`.
- `i = 5` (`S = 19`): candidates on hull: `j = 0`: `3.8`; `j = 1`: `18/4 = 4.5`; `j = 3`: `8/2 = 4`; `j = 4`: `2/1 = 2`. Best `j = 1`, answer `4`. ✓
- `i = 6` (`S = 24`): candidates: `j = 5` gives `5/1 = 5`, the best. Answer `1`. ✓
- `i = 7` (`S = 29`): candidates: `j = 5` gives `(29-19)/2 = 5`; `j = 6` gives `5/1 = 5`. Tie. Choose the smaller `j = 5`, answer `2`. ✓

Output: `1 1 2 1 4 1 2`. Matches.

## Pitfalls

- Using the upper hull. We want the steepest slope *into* the new point, which comes from the lower hull.
- Breaking ties toward the newest index. The problem wants the longest subarray, so the smallest `j`.
- Popping on strict turns only. Collinear hull points should be popped so the leftmost point of a straight run survives.
- Floating-point averages. Compare with cross multiplication on integers to avoid precision errors.
- Forgetting that the query uses only points `0..i-1`. Add point `i` to the hull after answering `i`.

## Complexity

- Time: `O(n log n)`.
- Space: `O(n)`.

## Key takeaway

A subarray average is a slope between prefix-sum points. The best earlier starting point for a new endpoint is always a vertex of the lower convex hull, so a monotone stack with binary search answers each query.
