# [1646 — Static Range Sum Queries](https://cses.fi/problemset/task/1646)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** prefix sums, range queries  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to process `q` queries of the form: what is the sum of values in range `[a,b]`?

### Input

The first input line has two integers `n` and `q`: the number of values and queries.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`: what is the sum of values in range `[a,b]`?

### Output

Print the result of each query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= xi <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
8 4
3 2 4 5 1 1 5 3
2 4
5 6
1 8
3 3
```

**Output**
```
11
2
24
4
```

---

## What this problem really is

Static array; many inclusive range-sum queries. Need `O(1)` per query after preprocessing (`n,q` up to `2e5`).

## What to use

| Tool | Why |
|------|-----|
| Prefix sums `pref[i]` | `pref[i] = x[1]+...+x[i]` |
| `long long` | Sums up to `2e5 * 1e9` |
| Formula `pref[b]-pref[a-1]` | Inclusive `[a,b]` sum |

## Approach

**Root idea:** precompute running totals so any segment sum is a difference of two prefixes.

**Mental picture:** a cumulative scoreboard; subtract the total before `a` from the total through `b`.

**Data structures**

- Array `x[1..n]`
- `pref[0]=0`, `pref[i]=pref[i-1]+x[i]`

**Steps**

1. Build `pref`.
2. For each query `(a,b)`, print `pref[b] - pref[a-1]`.

## Walkthrough (on a sample)

`x = [3,2,4,5,1,1,5,3]`

- `[2,4]` → `2+4+5 = 11`
- `[5,6]` → `1+1 = 2`
- `[1,8]` → `24`
- `[3,3]` → `4`

## Pitfalls

- Using `int` for sums — overflow.
- Off-by-one: ranges are **1-indexed inclusive**.
- Recomputing each query in `O(n)` — TLE.

## Complexity

- Time: `O(n + q)`  
- Space: `O(n)`

## Key takeaway

**Static range sum = prefix sums.** Answer `[a,b]` as `pref[b] - pref[a-1]`.
