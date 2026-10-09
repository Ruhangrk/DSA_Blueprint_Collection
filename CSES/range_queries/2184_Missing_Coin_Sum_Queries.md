# [2184 — Missing Coin Sum Queries](https://cses.fi/problemset/task/2184)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** persistent segment tree, missing coin sum, range queries  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have `n` coins with positive integer values. The coins are numbered `1, 2, ..., n`.

Your task is to process `q` queries of the form: “if you can use coins `a ... b`, what is the smallest sum you cannot produce?”

### Input

The first input line has two integers `n` and `q`: the number of coins and queries.

The second line has `n` integers `x1, x2, ..., xn`: the value of each coin.

Finally, there are `q` lines that describe the queries. Each line has two values `a` and `b`: you can use coins `a ... b`.

### Output

Print the answer for each query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= xi <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
5 3
2 9 1 2 7
2 4
4 4
1 5
```

**Output**
```
4
1
6
```

Explanation: First you can use coins `[9,1,2]`, then coins `[2]` and finally coins `[2,9,1,2,7]`.

---

## What this problem really is

Classic **missing coin sum** (smallest unreachable positive subset-sum), but on **many subarrays** `[a,b]` of the coin list — not on the full array once.

## What to use

| Tool | Why |
|------|-----|
| Jumping formula for missing sum | Avoid sorting the subarray each query |
| Persistent segtree on values | Sum of coin values `<= t` inside index range `[a,b]` |
| Coordinate compression | Values up to `1e9` |

## Approach

**Root idea (single array):** sort coins; keep `reach` = max such that all sums in `1..reach` are possible (`reach = 0` at start). For each coin `c` in order: if `c > reach+1`, answer is `reach+1`; else `reach += c`. Final answer `reach+1`.

**Root idea (queries):** the same answer can be found without an explicit sort by jumping a candidate `ans`:

```
ans = 1
while true:
  s = (sum of coins in [a,b] whose value is <= ans)
  if s < ans: return ans
  ans = s + 1
```

Intuition: coins `> ans` cannot help form `ans`. If the sum of the useful coins is `< ans`, you cannot form `ans`. If that sum is `s >= ans`, you can form everything through `s`, so the next candidate mex is `s+1`.

**Mental picture:** each jump asks “how much money do I have among coins that are small enough to matter?” and vaults the mex past that total.

**Data structures**

- Compress all `xi`.
- Persistent sum segtree over value ranks: version `i` = after inserting coin `i` (add `+xi` at rank of `xi`).
- `sumLeq(a, b, ans)` = total of values `<= ans` in index range = query value-range `[1 .. rank(ans)]` on `version[b]` minus `version[a-1]`.

**Steps**

1. Build persistent tree by scanning `i = 1..n`.
2. For each query `(a,b)`: run the jump loop using `sumLeq`; print final `ans`.
3. Iterations stay small in practice / `O(log)`-ish because `ans` moves to `s+1` with `s >= ans`.

## Walkthrough (on a sample)

`[2..4] = 9,1,2`:

- `ans=1`: coins `<=1` → `{1}`, `s=1` → `ans=2`
- `ans=2`: coins `<=2` → `{1,2}`, `s=3` → `ans=4`
- `ans=4`: coins `<=4` → `{1,2}`, `s=3 < 4` → return **4**

`[4..4] = 2`: `ans=1`, coins `<=1` empty → **1**.

`[1..5]`: continues until **6** (full classic missing sum).

## Pitfalls

- Summing counts instead of **values**.
- Using `<= ans-1` instead of `<= ans` in the jump (off-by-one breaks the invariant).
- `long long` for sums and `ans`.
- Building one static Fenwick over indices only — you also need a value threshold.

## Complexity

- Time: `O((n + q) * log V * I)` with small iteration count `I` per query  
- Space: `O(n log V)` persistent nodes

## Key takeaway

**Range missing-coin-sum = jump `ans` using “sum of values `<= ans` in `[a,b]`”** — support that sum with a persistent value segtree over prefixes.
