# [3138 — All Palindromes](https://cses.fi/problemset/task/3138)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** Manacher, palindromes  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string, calculate for each position the length of the longest palindrome that ends at that position.

### Input

The only line contains a string of length `n`. Each character is one of `a`–`z`.

### Output

Print `n` numbers: the length of each palindrome.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
ababbababaa
```

**Output**
```
1 1 3 3 2 4 6 8 5 5 2
```

---

## What this problem really is

For every index `i`, find `max` length `L` such that `s[i-L+1 .. i]` is a palindrome (always at least `1`). Not “longest palindrome in the whole string,” but longest one **ending exactly at `i`**.

## What to use

| Tool | Why |
|------|-----|
| Manacher radii `d1` / `d2` | All center palindromes in `O(n)` |
| Propagate centers to end positions | A palindrome centered at `c` with right end `r` also gives palindromes ending at every `j` in `[c..r]` (odd case) |
| Monotone pointer | Each `res[j]` is written once → keep total `O(n)` |

## Approach

**Root idea:** every palindrome ending at `j` is a suffix of some maximal palindrome that extends to (or past) `j`. Manacher finds all maximal odd/even palindromes; then push each center’s contribution rightward onto ending indices.

**Mental picture:** for an odd palindrome centered at `c` reaching right to `c+r`, the substring ending at `j` (`c <= j <= c+r`) that starts at `2*c-j` is still a palindrome — length `2*(j-c)+1`. Among all centers that can “cover” `j`, take the maximum length. Because larger (earlier) centers give longer lengths for a fixed `j`, a sweeping pointer updates each `j` only once.

**Steps**

1. Run Manacher → odd radii `d1[i]`, even radii `d2[i]`.
2. Initialize `ans[i] = 1` (single letters).
3. Odd pass: for each center `i`, for ending positions `j` from a monotone cursor through `i + d1[i] - 1`, set `ans[j] = 2*(j-i)+1`.
4. Even pass: similarly with lengths `2*(j-i+1)` from even centers.
5. Print `ans[0..n-1]`.

## Walkthrough (on a sample)

`ababbababaa` → ending lengths `1 1 3 3 2 4 6 8 5 5 2` as in the sample (e.g. near the long centered stretch you see growing even lengths like `8`).

## Pitfalls

- Answering longest palindrome **anywhere** ending ≤ `i` without the end-constraint.
- Nested loops over all centers and all `j` without a pointer — `O(n^2)`.
- Ignoring even-length palindromes.
- Off-by-one in radius → length conversion.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Longest palindrome ending at each index = Manacher radii + monotone propagation to right endpoints.**
