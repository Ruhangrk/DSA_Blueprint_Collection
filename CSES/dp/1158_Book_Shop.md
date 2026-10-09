# [1158 — Book Shop](https://cses.fi/problemset/task/1158)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, 0/1 knapsack  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are in a book shop which sells `n` different books. You know the price and number of pages of each book.

You have decided that the total price of your purchases will be at most `x`. What is the maximum number of pages you can buy? You can buy each book at most once.

### Input

The first input line contains two integers `n` and `x`: the number of books and the maximum total price.

The next line contains `n` integers `h1, h2, ..., hn`: the price of each book.

The last line contains `n` integers `s1, s2, ..., sn`: the number of pages of each book.

### Output

Print one integer: the maximum number of pages.

### Constraints

- `1 <= n <= 1000`
- `1 <= x <= 1e5`
- `1 <= hi, si <= 1000`

### Examples

**Input**
```
4 10
4 8 5 3
5 12 8 1
```

**Output**
```
13
```

### Note

You can buy books `1` and `3`. Their price is `4+5=9` and the number of pages is `5+8=13`.

---

## What this problem really is

Each book is an item with **cost** `hi` and **value** `si`. Pick a subset with total cost `<= x` maximizing total value. Each book at most once → classic **0/1 knapsack**.

## What to use

| Tool | Why |
|------|-----|
| `dp[0..x]` of `int` | `dp[j]` = max pages buyable with total price **exactly** `j` (or at most `j` — same if you take `max` over the array at the end) |
| 1D knapsack update | `n * x` fits in time; rolling array saves memory |
| Backward loop on budget | Ensures each book is used **at most once** |

## Approach

**Root idea:** decide books one by one. After considering a prefix of books, know the best page count for every possible money spent.

**Mental picture:** a row of money buckets `0 .. x`. Bucket `j` holds “best pages if I spent exactly `j`”. When a new book `(price h, pages s)` arrives, you may either skip it, or — for every bucket that already has a valid packing — pay `h` more and add `s` pages, landing in bucket `j + h`.

**Data structures**

- Arrays `h[n]`, `s[n]` for input.
- `dp[x+1]`: init `dp[0] = 0`, rest can start as `0` if we define “unreachable” as “0 pages and we only improve by taking books” (valid here because pages are positive; spending money with 0 pages is useless). Safer pattern: think “max pages among ways to spend **at most** `j`” and always take `max` when updating.

**0/1 update (critical):** for each book `i`, loop `j` from `x` down to `h[i]`:

```
dp[j] = max(dp[j], dp[j - h[i]] + s[i])
```

Going **high → low** means `dp[j - h[i]]` is still the value **before** using book `i`, so the book cannot be applied twice in the same pass.

**Steps**

1. Read `n`, `x`, prices, pages.
2. `dp` assign `0`.
3. For each book, backward-update `dp` as above.
4. Answer = maximum value in `dp[0..x]` (with the usual fill, `dp[x]` after “at most” style, or `max(dp[0], ..., dp[x])`).

If you store exact spend, answer is `max(dp[j])` for `j = 0 .. x`.

## Walkthrough (on a sample)

Books: `(4,5)`, `(8,12)`, `(5,8)`, `(3,1)`, budget `x = 10`.

Start: all `dp[j] = 0`.

**Book `(4,5)`** — for `j = 10 .. 4`: `dp[j] = max(dp[j], dp[j-4]+5)`.  
Now `dp[4]=5`, `dp[5]=5`, … (any `j >= 4` can hold at least those 5 pages from spending 4).

**Book `(8,12)`** — can set `dp[8] = 12`, `dp[12]` out of range; also `dp[8+4]=dp[12]` skip.  
`dp[8] = max(0, 12) = 12`.

**Book `(5,8)`** — interesting updates include:
- `j = 9`: `dp[9-5]+8 = dp[4]+8 = 5+8 = 13` → books 1 and 3  
- `j = 5`: `dp[0]+8 = 8`

**Book `(3,1)`** — small gains; does not beat `13` for this sample.

Best over budgets: **`13`**, matching the official note (`4+5` price, `5+8` pages).

## Pitfalls

- **Forward loop on `j`** → unbounded knapsack (same book reused) → WA.
- **`n=1000`, `x=1e5`** → `O(n*x)` is the limit; `O(n*x*x)` TLEs.
- Buying nothing is allowed → answer can be `0`.
- Do not sort or greedy by pages/price — counterexamples exist; DP is required.

## Complexity

- Time: `O(n * x)`  
- Space: `O(x)` for the rolling `dp` array

## Key takeaway

**0/1 knapsack:** one DP array over capacity; for each item, update capacity **from high to low** so each item is chosen at most once.
