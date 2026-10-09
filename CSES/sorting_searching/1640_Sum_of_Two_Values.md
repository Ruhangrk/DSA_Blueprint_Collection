# [1640 — Sum of Two Values](https://cses.fi/problemset/task/1640)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** hashing, two pointers, sorting  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers, and your task is to find two values (at distinct positions) whose sum is `x`.

### Input

The first input line has two integers `n` and `x`: the array size and the target sum.

The second line has `n` integers `a1, a2, ..., an`: the array values.

### Output

Print two integers: the positions of the values. If there are several solutions, you may print any of them. If there are no solutions, print `IMPOSSIBLE`.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x, ai <= 1e9`

### Examples

**Input**
```
4 8
2 7 5 1
```

**Output**
```
2 4
```

---

## What this problem really is

Find indices `i != j` with `a[i] + a[j] = x` (1-based positions in the output).

## What to use

| Tool | Why |
|------|-----|
| Hash map `value -> index` | One pass: for each `a[i]`, look up `x - a[i]` |
| Sort + two pointers (with indices) | Alternative `O(n log n)` without hashing |
| Keep original indices | Output needs positions, not values |

## Approach

**Root idea:** while scanning left to right, every earlier value is already in a map. For the current value `v`, if `x-v` was seen, those two positions work.

**Mental picture:** a notebook of “numbers I’ve already met and where”; at each new number, check if its partner was noted.

**Data structures**

- `map` or `unordered_map` from value → one index (1-based).
- Or: vector of `{value, index}`, sort by value, two pointers `L,R`.

**Hash-map steps**

1. For `i = 1..n`:
   - `need = x - a[i]`.
   - If `need` is in the map, print `map[need]` and `i`.
   - Else store `a[i] -> i` (only need one index per value for this problem if duplicates: if `2*a[i]=x`, you need two different indices — insert **after** the lookup so the same position isn’t reused).
2. If none found: `IMPOSSIBLE`.

**Two-pointer sketch:** sort pairs by value; `L=0`, `R=n-1`; move `L` up if sum too small, `R` down if too big; skip when `L==R`.

## Walkthrough (on a sample)

`x=8`, array `2,7,5,1`.

- `i=1` (`2`): need `6` — miss; store `2→1`.
- `i=2` (`7`): need `1` — miss; store `7→2`.
- `i=3` (`5`): need `3` — miss; store `5→3`.
- `i=4` (`1`): need `7` — found at index `2` → print **`2 4`**.

## Pitfalls

- Using the same index twice when `2 * a[i] = x` (lookup before insert).
- Printing 0-based indices.
- `unordered_map` worst-case hashing issues — rare; `map` is safer if TL allows.
- Forgetting `IMPOSSIBLE` when no pair exists.

## Complexity

- Time: expected `O(n)` with hash map, or `O(n log n)` with sort  
- Space: `O(n)`

## Key takeaway

**Two-sum with positions = one-pass map of seen values** (look up `x - a[i]` before inserting `a[i]`).
