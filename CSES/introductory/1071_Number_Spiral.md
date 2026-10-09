# [1071 — Number Spiral](https://cses.fi/problemset/task/1071)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** math, formula, layers  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A number spiral is an infinite grid whose upper-left square has number `1`. Here are the first five layers of the spiral (see CSES figure).

Your task is to find out the number in row `y` and column `x`.

### Input

The first input line contains an integer `t`: the number of tests.

After this, there are `t` lines, each containing integers `y` and `x`.

### Output

For each test, print the number in row `y` and column `x`.

### Constraints

- `1 <= t <= 1e5`
- `1 <= y, x <= 1e9`

### Examples

**Input**
```
3
2 3
1 1
4 2
```

**Output**
```
8
1
15
```

---

## What this problem really is

Closed-form lookup in a Ulam-style spiral layered by `max(y,x)`. Coordinates up to `1e9` and `t` up to `1e5` forbid building the grid.

## What to use

| Tool | Why |
|------|-----|
| Layer `m = max(y,x)` | Cell `(y,x)` lies on the `m x m` border |
| Corner value `m*m` | End of layer `m` |
| Parity of `m` | Direction of the spiral on that layer flips |
| `long long` | `m*m` up to `1e18` |

## Approach

**Root idea:** on layer `m`, numbers fill either along the bottom/right edges depending on whether `m` is odd or even. Derive the value from the distance to the corner `m^2`.

**Mental picture:** square onion rings; find which ring, then walk along its exposed L-shaped edge from the known corner.

**Formula** (1-based rows/cols):

```
if (y >= x) {
  if (y % 2 == 0) ans = y*y - x + 1;
  else            ans = (y-1)*(y-1) + x;
} else {
  if (x % 2 == 0) ans = (x-1)*(x-1) + y;
  else            ans = x*x - y + 1;
}
```

**Intuition:** when the larger coordinate is even/odd, the layer fills the opposite way along the exposed L from the previous square `(m-1)^2`.

**Steps**

1. For each query, compute with `long long` as above.
2. Print `ans`.

## Walkthrough (on a sample)

`(2,3)`: `x > y`, `x` odd → `x*x - y + 1 = 9 - 2 + 1 = **8**`.

`(1,1)` → **1**. `(4,2)`: `y > x`, `y` even → `16 - 2 + 1 = **15**`.

## Pitfalls

- Mixing up row/column or odd/even branches.
- `int` overflow on `m*m`.
- Simulating the spiral for each query — TLE.

## Complexity

- Time: `O(t)`  
- Space: `O(1)`

## Key takeaway

**Number spiral queries = closed form on layer `m = max(y,x)`** with parity deciding which edge formula to use.
