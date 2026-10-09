# [1731 — Word Combinations](https://cses.fi/problemset/task/1731)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** trie, DP, word break  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a string of length `n` and a dictionary containing `k` words. In how many ways can you create the string using the words?

### Input

The first input line has a string containing `n` characters between `a`–`z`.

The second line has an integer `k`: the number of words in the dictionary.

Finally there are `k` lines describing the words. Each word is unique and consists of characters `a`–`z`.

### Output

Print the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n <= 5000`
- `1 <= k <= 1e5`
- the total length of the words is at most `1e6`

### Examples

**Input**
```
ababc
4
ab
abab
c
cb
```

**Output**
```
2
```

Explanation: The possible ways are `ab+ab+c` and `abab+c`.

---

## What this problem really is

Count ordered concatenations of dictionary words that equal the target string (word-break counting), modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| Trie of dictionary words | Fast “all words starting at position `i`” without hashing every substring |
| `dp[i]` = ways to form suffix `s[i..]` | Classic segmentation DP |
| `dp[n] = 1` | Empty suffix has one way |

## Approach

**Root idea:** from index `i`, every dictionary word that matches a prefix of `s[i..]` can be peeled off; add the ways to solve the remainder.

**Mental picture:** walk along the string; at each spot, look up the trie like a dictionary autocomplete and whenever you hit a word end, jump ahead and multiply/add possibilities.

**Steps**

1. Insert every dictionary word into a trie; mark terminal nodes.
2. `dp[n] = 1`; for `i = n-1 .. 0`:
   - Start at trie root; walk `j = i, i+1, ...` along `s[j]`.
   - If the edge is missing, stop.
   - If the current node is a word end, add `dp[j+1]` to `dp[i]` (mod `1e9+7`).
3. Print `dp[0]`.

Naive “try all substrings in a hash set” is too slow with large dictionaries; the trie shares prefixes and stops early on mismatches. Worst case still `O(n^2)` character steps, which is OK for `n <= 5000`.

## Walkthrough (on a sample)

`s = ababc`.

- From index 0: word `ab` → need ways for `abc`; word `abab` → need ways for `c`.
- `c` is a dictionary word → `dp` for that suffix is 1.
- `ab` + `ab` + `c` and `abab` + `c` → total 2.

## Pitfalls

- Counting unordered sets of words — order of concatenation is fixed by positions; different partitions are different ways.
- Overlapping word ends in the trie — must add whenever a terminal is hit mid-walk, not only at leaves.
- Forgetting `MOD`.
- Building a set and checking every `s[i..j]` with string hashing can still TLE if not careful; trie is the intended structure.

## Complexity

- Time: `O(n^2 + total dictionary length)`  
- Space: `O(total dictionary length + n)`

## Key takeaway

**Word-break count = trie + suffix DP:** from each index, walk the trie and add `dp[j+1]` at every word end.
