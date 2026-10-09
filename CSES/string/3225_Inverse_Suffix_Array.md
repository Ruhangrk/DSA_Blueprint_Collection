# [3225 — Inverse Suffix Array](https://cses.fi/problemset/task/3225)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** suffix array, reconstruction, greedy  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a suffix array of a string, your task is to reconstruct the string.

The suffix array of a string of length `n` is a permutation of numbers `1, 2, ..., n` that presents the lexicographical order of the suffixes.

### Input

The first line has an integer `n`: the length of the string.

The next line has `n` integers: the suffix array.

### Output

Print a string that corresponds to the suffix array. The string must consist of characters `a`–`z`. If there are several possible strings, you can print any of them.

If no string corresponds to the suffix array, print `-1`.

### Constraints

- `1 <= n <= 1e5`

### Examples

**Input**
```
7
4 1 3 5 6 7 2
```

**Output**
```
aybabtu
```

---

## What this problem really is

Given the order of all suffixes, invent any string over `a`–`z` whose suffix array is exactly that permutation — or prove none exists (more than 26 forced strict increases).

## What to use

| Tool | Why |
|------|-----|
| Ranks `ord[pos]` | Inverse of SA: order of suffix starting at `pos` |
| Adjacent SA constraints | Only consecutive suffixes in SA order need local first-char decisions |
| Greedy letter assignment | Reuse the same letter whenever the remaining suffixes already enforce order |

## Approach

**Root idea:** walk suffixes in SA order. For two consecutive suffixes `A = s[sa[i]..]` and `B = s[sa[i+1]..]`, either:

- their first characters can be **equal**, and the order still holds because `suffix(sa[i]+1) < suffix(sa[i+1]+1)` (or `A` is the single last character — empty continuation is smallest), or  
- the first character of `B` must be **strictly larger** than that of `A`.

Assign the smallest alphabet letter that respects these forced increases.

**Mental picture:** SA is a sorted list of tails. Neighbors that already differ later can share the same opening letter; neighbors that would tie forever need a bump in the alphabet.

**Steps** (0-based positions in code; input SA is 1-based)

1. Read `sa[0..n-1]` (convert to 0-based). Build `ord[sa[i]] = i`.
2. `letter = 0`; set `s[sa[0]] = 'a'`.
3. For `i = 1 .. n-1`:
   - Let `p = sa[i-1]`, `q = sa[i]`.
   - `same_ok = false`.
   - If `p+1 < n` and `q+1 < n` and `ord[p+1] < ord[q+1]` → equal first chars still ordered → `same_ok = true`.
   - If `p+1 == n` (suffix `p` has length 1) → empty rest is smallest → `same_ok = true`.
   - If not `same_ok`: `letter++`.
   - If `letter >= 26` → print `-1`.
   - `s[q] = 'a' + letter`.
4. Optional but safe: rebuild the SA of `s` and verify it matches; if not, `-1`.
5. Print `s`.

## Walkthrough (on a sample)

SA `4 1 3 5 6 7 2` (1-based) reconstructs to `aybabtu` — letters rise only when consecutive suffixes demand a strict first-char increase.

## Pitfalls

- Always incrementing the letter between SA neighbors — uses too many letters / wrong string.
- Forgetting the length-1 suffix special case.
- Not verifying impossibility when `letter` exceeds `'z'`.
- 1-based vs 0-based SA positions.

## Complexity

- Time: `O(n)` construct (+ `O(n log n)` if you verify with SA)  
- Space: `O(n)`

## Key takeaway

**Inverse SA = greedy letters along SA order** — bump the character only when the next suffix is not already smaller via its tail.
