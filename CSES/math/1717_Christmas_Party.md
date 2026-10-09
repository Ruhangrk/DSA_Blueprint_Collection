# [1717 — Christmas Party](https://cses.fi/problemset/task/1717)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** derangements, combinatorics  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` children at a Christmas party, and each of them has brought a gift. The idea is that everybody will get a gift brought by someone else.

In how many ways can the gifts be distributed?

### Input

The only input line has an integer `n`: the number of children.

### Output

Print the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
4
```

**Output**
```
9
```

---

## What this problem really is

Count **derangements** `!n`: permutations of `n` gifts with no fixed points (nobody gets their own gift).

## What to use

| Tool | Why |
|------|-----|
| Recurrence `!n = (n-1) * (!(n-1) + !(n-2))` | Classic derangement recurrence |
| Or `!n = n! * sum (-1)^i / i!` | Series form with modular inverses |
| `MOD = 1e9+7` | Required |

## Approach

**Root idea:** for a derangement, item `n` goes to some position `i`, and either `i`’s item goes to `n` (swap) or not (then treat as derangement of `n-1` after a “cycle join”). That yields:

```
d[0] = 1; d[1] = 0;
d[i] = (i-1) * (d[i-1] + d[i-2]) % MOD
```

**Mental picture:** nobody keeps their own present — count permitted gift cycles covering everyone.

**Steps**

1. If `n == 1` print `0`.
2. Iterate the recurrence up to `n`.
3. Print `d[n]`.

## Walkthrough (on a sample)

`!4 = 9` (known derangement count).

## Pitfalls

- Using `n!` (allows fixed points).
- `d[1]=1` by mistake — should be `0`.

## Complexity

- Time: `O(n)`  
- Space: `O(1)` with rolling variables

## Key takeaway

**Gift exchange with no own-gift = derangement `!n`** — use the `(n-1)(!(n-1)+!(n-2))` recurrence mod `1e9+7`.
