# [2163 — Josephus Problem II](https://cses.fi/problemset/task/2163)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** ordered set, Fenwick / segment tree, josephus  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a game where there are `n` children (numbered `1, 2, ..., n`) in a circle. During the game, repeatedly `k` children are skipped and one child is removed from the circle. In which order will the children be removed?

### Input

The only input line has two integers `n` and `k`.

### Output

Print `n` integers: the removal order.

### Constraints

- `1 <= n <= 2e5`
- `0 <= k <= 1e9`

### Examples

**Input**
```
7 2
```

**Output**
```
3 6 2 7 5 1 4
```

---

## What this problem really is

Josephus with general skip count `k`: from the current position, skip `k` living children, remove the next. Report full removal order. `k` can be huge (`1e9`), so you always reduce modulo the **current** number of living children.

## What to use

| Tool | Why |
|------|-----|
| Order-statistic structure | Find/delete the `idx`-th remaining child in `O(log n)` |
| Fenwick / segtree of `1`s | Index `i` alive → `1`; prefix sums = rank |
| `gnu pbds indexed_set` | Same role if available |
| `pos = (pos + k) % alive` | Next victim index among remaining |

## Approach

**Root idea:** keep children in a circle logically by rank among survivors. If `alive` children remain and you stand before rank `pos` (0-based), the removed index is `(pos + k) % alive`. Delete that child; the next search continues from the same rank (the old next child slides into that slot).

**Mental picture:** a dynamic numbered list of survivors; each round you count `k+1` steps on that list (because `k` skips then one removal), erase, and keep counting from the next.

**Data structures**

- Fenwick/segtree on `n` positions, initially `1` at each child (alive).
- Or policy-based `indexed_set` of ids `1..n`.

**Steps (0-based rank)**

1. `alive = n`, `pos = 0` (start counting from child 1).
2. Repeat `n` times:
   - `pos = (pos + k) % alive`
   - Find the value/id at rank `pos` among alive; print it; delete it.
   - `alive--` (do **not** increment `pos` — the next child now sits at this rank).
3. Done.

Finding rank `pos`: binary lift on Fenwick (`find_kth(pos+1)`) or `*set.find_by_order(pos)`.

## Walkthrough (on a sample)

`n=7`, `k=2`. Living `1..7`. Start `pos=0`.

- `(0+2)%7=2` → 3rd child = **3**; remove; living `1,2,4,5,6,7`; still at rank 2 → now **4**…
- Continue: removals **3, 6, 2, 7, 5, 1, 4**.

(Queue simulation would be `O(n * (k % n))` — too slow when `k` and `n` are both large.)

## Pitfalls

- Not taking `k % alive` (equiv. via `(pos + k) % alive`) — TLE / wrong when `k` is huge.
- After deletion, advancing `pos` by one extra — skips the wrong child.
- Building an `O(n)` list and erasing in the middle each time — TLE.
- `k = 0`: remove the current child every time (still fine with the formula).

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**General Josephus order = order-statistic tree / Fenwick of alive ranks** with `pos = (pos + k) % alive`, delete, repeat.
