# [2189 — Point Location Test](https://cses.fi/problemset/task/2189)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** cross product, orientation  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a line that goes through the points `p1 = (x1, y1)` and `p2 = (x2, y2)`. There is also a point `p3 = (x3, y3)`.

Your task is to determine whether `p3` is located on the left or right side of the line or if it touches the line when we are looking from `p1` to `p2`.

### Input

The first input line has an integer `t`: the number of tests.

After this, there are `t` lines that describe the tests. Each line has six integers: `x1`, `y1`, `x2`, `y2`, `x3` and `y3`.

### Output

For each test, print `"LEFT"`, `"RIGHT"` or `"TOUCH"`.

### Constraints

- `1 <= t <= 1e5`
- `-1e9 <= x1, y1, x2, y2, x3, y3 <= 1e9`
- `x1 != x2` or `y1 != y2`

### Examples

**Input**
```
3
1 1 5 3 2 3
1 1 5 3 4 1
1 1 5 3 3 2
```

**Output**
```
LEFT
RIGHT
TOUCH
```

---

## What this problem really is

2D orientation test: standing at `p1` looking toward `p2`, is `p3` left, right, or on the directed line?

## What to use

| Tool | Why |
|------|-----|
| Cross product of vectors | Sign = turn direction |
| 64-bit integers | Products of coordinates up to `1e9` need `__int128` or careful `long long` (diffs fit in `ll` products up to `~4e18`, borderline — use `__int128` or check) |

## Approach

**Root idea:** vector `A = p2 - p1`, vector `B = p3 - p1`. The signed area `A.x * B.y - A.y * B.x` is positive for a left turn, negative for a right turn, zero if collinear.

**Mental picture:** the parallelogram spanned by `A` and `B`. Positive orientation = counterclockwise = left when walking along `A`.

**Steps**

1. `cross = (x2-x1)*1LL*(y3-y1) - (y2-y1)*1LL*(x3-x1)` (use wider type if needed).
2. `cross > 0` → `LEFT`; `cross < 0` → `RIGHT`; else `TOUCH`.

## Walkthrough (on a sample)

From `(1,1)` to `(5,3)`:

- `(2,3)`: left of the ray → `LEFT`.  
- `(4,1)`: right → `RIGHT`.  
- `(3,2)`: on the line → `TOUCH`.

## Pitfalls

- Using `double` — precision WA.  
- 32-bit overflow on the multiply.  
- Swapping left/right (wrong cross order).  
- Treating undirected line — direction `p1 -> p2` matters.

## Complexity

- Time: `O(t)`  
- Space: `O(1)`

## Key takeaway

**Left/right/on-line = sign of cross `(p2-p1) x (p3-p1)`.**
