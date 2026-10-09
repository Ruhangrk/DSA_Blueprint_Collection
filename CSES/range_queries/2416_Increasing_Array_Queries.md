# [2416 — Increasing Array Queries](https://cses.fi/problemset/task/2416)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** offline queries, monotonic stack, Fenwick / segment tree, prefix maxima  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array that consists of `n` integers. The array elements are indexed `1, 2, ..., n`.

You can modify the array using the following operation: choose an array element and increase its value by one.

Your task is to process `q` queries of the form: when we consider a subarray from position `a` to position `b`, what is the minimum number of operations after which the subarray is increasing?

An array is increasing if each element is greater than or equal with the previous element.

### Input

The first input line has two integers `n` and `q`: the size of the array and the number of queries.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

Finally, there are `q` lines that describe the queries. Each line has two integers `a` and `b`: the starting and ending position of a subarray.

### Output

For each query, print the minimum number of operations.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= xi <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
5 3
2 10 4 2 5
3 5
2 2
1 4
```

**Output**
```
2
0
14
```

---

## What this problem really is

Only **increases** allowed. For each query subarray `[a,b]`, minimum total increments to make it **non-decreasing**. Static queries, large `n,q`.

## What to use

| Tool | Why |
|------|-----|
| Closed form via prefix maxima | Optimal final value at `i` is `max(x[a..i])` |
| Prefix sums of `x` | Subtract `sum(x[a..b])` from sum of those maxima |
| Offline + monotonic stack + Fenwick/segtree | Answer `sum of max(x[a..i])` for many `[a,b]` fast |

## Approach

**Root idea:** you never want to raise an element above the running maximum forced by the left side. For fixed `a`, the cheapest non-decreasing completion on `[a..i]` sets position `i` to `M[i] = max(x[a], x[a+1], ..., x[i])`. Cost on `[a,b]` is:

```
sum_{i=a..b} (M[i] - x[i]) = sum M[i] - sum x[i]
```

So the only hard part is range queries of **sum of prefix-maxima** starting at `a`.

**Mental picture:** from `a` walking right, the “ceiling” is a staircase that only jumps up when a new record high appears. Between records, every cell is lifted up to the current record. Total ops = area between the staircase and the original bars.

**Data structures (offline by decreasing `a`)**

- Process left endpoints from `n` down to `1`.
- Maintain a stack of indices to the right with **strictly decreasing** `x` (candidates for being a record when the left end moves).
- A Fenwick/segtree over positions storing how much the current staircase contributes per index (or difference-encoded contributions), so a query with right end `b` is a range sum `[a,b]`.
- When the left end becomes `a`, push `a` onto the structure: pop rightward positions that are `<= x[a]` (they are dominated as records), and rewrite their contribution so the new plateau height is `x[a]` until the next greater element.

**Also keep** prefix sums `pref[i] = x[1]+...+x[i]` for the `sum x[a..b]` term.

**Steps**

1. Precompute `pref`.
2. Store queries `(a,b,id)`; group or sort so you can answer when left end is `a`.
3. Sweep `a = n..1`, maintain staircase contributions in a BIT/segtree; for each query with that `a`, answer = `rangeSumContrib(a,b) - (pref[b]-pref[a-1])`.
4. Output in original order.

(Equivalent view: next-greater links + binary lifting on contribution along the record chain — same formula, different implementation.)

## Walkthrough (on a sample)

Array: `2, 10, 4, 2, 5`.

`[3,5]`: maxima walk `4, max(4,2)=4, max(4,5)=5` → costs `0, 2, 0` → **2**.

`[2,2]`: single element → **0**.

`[1,4]`: maxima `2, 10, 10, 10` → costs `0, 0, 6, 8` → **14**.

The staircase for left=`1` is height `2` then jumps to `10` and stays; ops are exactly the gaps under that staircase.

## Pitfalls

- Making the array **strictly** increasing (`>`) — problem allows `>=`.
- Decreasing elements (not allowed) — only `+1` ops.
- Computing `max` of the whole subarray once — wrong; each prefix needs its own running max.
- `long long` for sums (`xi` up to `1e9`, length `2e5`).

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n + q)`

## Key takeaway

**Min increments to make `[a,b]` non-decreasing = `sum (max(x[a..i]) - x[i])`** — answer with prefix sums plus an offline staircase / next-greater structure for the sum of those maxima.
