# [2431 — Digit Queries](https://cses.fi/problemset/task/2431)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** math, digit DP style counting  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider an infinite string that consists of all positive integers in increasing order:

`12345678910111213141516171819202122232425...`

Your task is to process `q` queries of the form: what is the digit at position `k` in the string?

### Input

The first input line has an integer `q`: the number of queries.

After this, there are `q` lines that describe the queries. Each line has an integer `k`: a 1-indexed position in the string.

### Output

For each query, print the corresponding digit.

### Constraints

- `1 <= q <= 1000`
- `1 <= k <= 1e18`

### Examples

**Input**
```
3
7
19
12
```

**Output**
```
7
4
1
```

---

## What this problem really is

The Champernowne concatenation of `1,2,3,...`. Answer the digit at huge index `k` (up to `1e18`) — cannot build the string.

## What to use

| Tool | Why |
|------|-----|
| Group by digit-length | 1-digit, 2-digit, … blocks have closed-form sizes |
| `long long` | Indices and counts up to `1e18` |
| Per-query loop over length | `q` small; lengths up to ~18 |

## Approach

**Root idea:** skip whole blocks of fixed length until `k` falls inside a block, then find which number and which digit.

- Length `len = 1,2,3,...`
- Count of `len`-digit numbers: `9 * 10^(len-1)` (for `len=1`: nine numbers `1..9`; note `0` is not written).
- Digits in that block: `cnt * len`.

**Steps for one `k`**

1. `len = 1`, `block = 9`.
2. While `k > block * len`: `k -= block * len`; `len++`; `block *= 10`.
3. Now `k` is 1-based inside the `len`-digit block. Number index = `(k-1) / len` (0-based among `len`-digit numbers). Starting number = `10^(len-1)` (or `1` when `len==1`).
4. `number = start + (k-1)/len`. Digit offset = `(k-1) % len`.
5. Print that digit of `number`.

**Mental picture:** shelves of books by page-width (digit length); skip full shelves, open the right book, pick the digit.

## Walkthrough (on a sample)

`k=7`: still in 1-digit block (`9` digits) → digit of number `7` → **7**.

`k=12`: past `9` ones → `k'=3` in 2-digit block → numbers `10,11,...`; `(3-1)/2=1` → number `11`, offset `0` → **1**.

`k=19`: `k'=10` in 2-digit → number `10 + 4 = 14`, offset `1` → **4**.

## Pitfalls

- Including `0` as a 1-digit contribution.
- Overflow when computing `block * len` — compare carefully / use `__int128` or subtract in a safe order.
- 0-based vs 1-based `k` when indexing digits.

## Complexity

- Time: `O(q * log k)`  
- Space: `O(1)`

## Key takeaway

**Digit in Champernowne string = skip length-blocks, then locate number + digit offset** — no string building.
