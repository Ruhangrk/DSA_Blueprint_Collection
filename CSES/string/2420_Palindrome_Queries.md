# [2420 — Palindrome Queries](https://cses.fi/problemset/task/2420)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** hashing, Fenwick / segment tree, palindromes  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a string that consists of `n` characters between `a`–`z`. The positions of the string are indexed `1, 2, ..., n`.

Your task is to process `m` operations of the following types:

1. Change the character at position `k` to `x`
2. Check if the substring from position `a` to position `b` is a palindrome

### Input

The first input line has two integers `n` and `m`: the length of the string and the number of operations.

The next line has a string that consists of `n` characters.

Finally, there are `m` lines that describe the operations. Each line is of the form `"1 k x"` or `"2 a b"`.

### Output

For each operation `2`, print `YES` if the substring is a palindrome and `NO` otherwise.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= k <= n`
- `1 <= a <= b <= n`

### Examples

**Input**
```
7 5
aybabtu
2 3 5
1 3 x
2 3 5
1 5 x
2 3 5
```

**Output**
```
YES
NO
YES
```

---

## What this problem really is

Dynamic string: point updates + many “is `s[a..b]` a palindrome?” queries. Need something faster than rebuilding or expanding each time.

## What to use

| Tool | Why |
|------|-----|
| Polynomial rolling hash | Compare a substring to its reverse in `O(1)` after prep |
| Forward + reverse hash structures | Palindrome ⇔ forward hash of `[a,b]` equals reverse hash of `[a,b]` (aligned) |
| Fenwick / segment tree | Point update + range hash combine in `O(log n)` |

## Approach

**Root idea:** `s[a..b]` is a palindrome iff it equals its reverse. Maintain a hash of the string left-to-right and a hash as if the string were reversed (or hash of the reversed index order). After aligning powers, equality of the two range hashes means yes.

**Mental picture:** two number lines of powers — one growing from the left, one from the right. A segment is a palindrome when the “weight pattern” matches forwards and backwards.

**Data**

- `pre`: Fenwick/seg storing contribution `(s[i]-'a'+1) * B^{i}` (1-based).
- `suf`: same with `B^{n-i+1}` (or hash the reversed string in a second tree).
- Powers `B^0 .. B^n` mod a large prime (double hash optional for safety).

**Operations**

1. Update position `k` to `x`: remove old contribution, add new, in both trees; update `s[k]`.
2. Query `[a,b]`: extract forward hash of `[a,b]` and reverse hash of `[a,b]`; multiply by powers so both are normalized to the same offset; print `YES` / `NO`.

Odd/even length both work — full segment compare, no special casing.

## Walkthrough (on a sample)

`aybabtu`: query `3..5` is `bab` → `YES`.  
Change index `3` to `x` → `axb` → `NO`.  
Change index `5` to `x` → `axx`… wait sample: after first update string is `ayxabtu`, query `3..5` = `xab` → `NO`; then set position `5` to `x` → `ayxax tu` query `3..5` = `xax` → `YES`.

## Pitfalls

- Comparing hashes without aligning powers (different starting exponents).
- Updating only the forward structure.
- Single mod with bad base — rare collisions; random base / double mod helps.
- 0-based vs 1-based Fenwick indices.

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n)`

## Key takeaway

**Dynamic palindrome checks = forward & reverse range hashes in a Fenwick/seg tree** — update both on edits, compare on queries.
