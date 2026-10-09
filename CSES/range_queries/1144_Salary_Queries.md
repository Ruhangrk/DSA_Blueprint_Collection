# [1144 — Salary Queries](https://cses.fi/problemset/task/1144)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** Fenwick / segment tree, coordinate compression  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A company has `n` employees with certain salaries. Your task is to keep track of the salaries and process queries.

### Input

The first input line contains two integers `n` and `q`: the number of employees and queries. The employees are numbered `1, 2, ..., n`.

The next line has `n` integers `p1, p2, ..., pn`: each employee's salary.

After this, there are `q` lines describing the queries. Each line has one of the following forms:

- `! k x`: change the salary of employee `k` to `x`
- `? a b`: count the number of employees whose salary is between `a ... b`

### Output

Print the answer to each `?` query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= pi <= 1e9`
- `1 <= k <= n`
- `1 <= x <= 1e9`
- `1 <= a <= b <= 1e9`

### Examples

**Input**
```
5 3
3 7 2 2 5
? 2 3
! 3 6
? 2 3
```

**Output**
```
3
2
```

---

## What this problem really is

Multiset of salaries with point updates and **count in value range `[a,b]`**. Values up to `1e9` → compress; need `O(log n)` ops.

## What to use

| Tool | Why |
|------|-----|
| Coordinate compression | Map all appearing salaries (initial + updates + query bounds) to ranks |
| Fenwick of frequencies | `add(rank, +1/-1)` on salary change; range count = prefix diff |
| Keep `sal[k]` | Know old salary when updating employee `k` |

## Approach

**Root idea:** frequency array on compressed salary ranks. Changing employee `k` from old→new: decrement old rank, increment new. Count `[a,b]` = sum of frequencies with rank in `[rank(a), rank(b)]`.

**Mental picture:** histogram of salaries; queries ask “how many bars between two pay levels?”

**Steps**

1. Collect all values: initial `p`, all `x` from `!`, and all `a,b` from `?` (so bounds exist).
2. Sort unique → ranks.
3. BIT: for each initial salary, `add(rank(p[i]), 1)`.
4. `! k x`: `add(rank(sal[k]), -1)`; `sal[k]=x`; `add(rank(x), +1)`.
5. `? a b`: print `sum(rank(b)) - sum(rank(a)-1)`.

## Walkthrough (on a sample)

Salaries `3,7,2,2,5`. In `[2,3]` → three people (`2,2,3`). Change employee `3` from `2` to `6`; then `[2,3]` → two (`2,3`).

## Pitfalls

- Not compressing query endpoints — can’t index `1e9`.
- Updating without removing the old salary count.
- Inclusive bounds: use `rank` of largest value `<= b` carefully (include all salaries in `[a,b]`).

## Complexity

- Time: `O((n+q) log (n+q))`  
- Space: `O(n+q)`

## Key takeaway

**Salary range counts with updates = compressed Fenwick of frequencies.** Point move = `-1` old / `+1` new; query = prefix difference on ranks.
