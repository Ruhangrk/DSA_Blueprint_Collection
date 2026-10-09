# [1650 — Range Xor Queries](https://cses.fi/problemset/task/1650)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** prefix xor, range queries  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to process `q` queries of the form: what is the xor sum of values in range `[a,b]`?

### Input

The first input line has two integers `n` and `q`: the number of values and queries.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`: what is the xor sum of values in range `[a,b]`?

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
3
0
6
4
```

---

## What this problem really is

Static range XOR queries. Same structure as prefix sums, but with XOR.

## What to use

| Tool | Why |
|------|-----|
| Prefix XOR `px[i]` | `px[i] = x[1] xor ... xor x[i]` |
| Identity `a xor a = 0` | `px[b] xor px[a-1]` cancels the prefix before `a` |

## Approach

**Root idea:** XOR is its own inverse, so range XOR is prefixXOR difference (via XOR, not subtraction).

**Mental picture:** running XOR parity of bits; “subtract” a prefix by XORing it again.

**Steps**

1. `px[0]=0`; `px[i]=px[i-1] xor x[i]`.
2. Query `[a,b]` → `px[b] xor px[a-1]`.

## Walkthrough (on a sample)

`[2,4]`: `2 xor 4 xor 5 = 3`; `[5,6]`: `1 xor 1 = 0`; full → `6`; `[3,3]` → `4`.

## Pitfalls

- Using `+`/`-` instead of XOR.
- Off-by-one on `a-1`.

## Complexity

- Time: `O(n + q)`  
- Space: `O(n)`

## Key takeaway

**Static range XOR = prefix XOR.** Answer `[a,b]` as `px[b] xor px[a-1]`.
