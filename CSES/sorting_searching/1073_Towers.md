# [1073 — Towers](https://cses.fi/problemset/task/1073)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** multiset, greedy, binary search  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given `n` cubes in a certain order, and your task is to build towers using them. Whenever two cubes are one on top of the other, the upper cube must be smaller than the lower cube.

You must process the cubes in the given order. You can always either place the cube on top of an existing tower, or begin a new tower. What is the minimum possible number of towers?

### Input

The first input line contains an integer `n`: the number of cubes.

The next line contains `n` integers `k1, k2, ..., kn`: the sizes of the cubes.

### Output

Print one integer: the minimum number of towers.

### Constraints

- `1 <= n <= 2e5`
- `1 <= ki <= 1e9`

### Examples

**Input**
```
5
3 8 2 1 5
```

**Output**
```
2
```

---

## What this problem really is

Process cubes online. Each tower is represented by its **top** (smallest cube so far on that tower — the only place you can stack). Place each cube on some top that is **strictly larger**, or open a new tower. Minimize towers.

## What to use

| Tool | Why |
|------|-----|
| `multiset` of tower tops | Find the smallest top `> k` (upper bound) |
| Greedy | Always put the cube on the **tightest** larger top — leaves bigger tops free |
| Why multiset | Many towers; need `O(log n)` search + replace |

## Approach

**Root idea:** among tops that can accept `k` (tops `> k`), choose the smallest such top. Replace that top with `k`. If none exists, start a new tower with top `k`.

**Mental picture:** each tower shows only its tip size. Incoming cube looks for the gentlest tip it can sit under (smallest tip still bigger than itself).

**Data structures**

- `multiset<int> tops` — current top of each tower.

**Steps**

1. For each cube `k` in order:
   - `auto it = tops.upper_bound(k);` — first top **strictly greater** than `k`.
   - If `it == tops.end()`: `tops.insert(k)` (new tower).
   - Else: erase `it`, insert `k` (stack on that tower; new tip is `k`).
2. Answer = `tops.size()`.

## Walkthrough (on a sample)

`3` → tops `{3}`.

`8` → no top `> 8` → `{3,8}`.

`2` → upper_bound → `3`; replace → `{2,8}`.

`1` → replace `2` → `{1,8}`.

`5` → replace `8` → `{1,5}`.

**2** towers.

## Pitfalls

- Using `>=` instead of strict `>` (upper cube must be **smaller**).
- Always creating a new tower when any top works — wastes towers.
- Picking the largest available top instead of the smallest feasible — suboptimal.
- `set` instead of `multiset` if equal tops can exist.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Min towers with decreasing tops = multiset of tops; place each cube on `upper_bound(k)` (tightest larger tip) or open a new tower.**
