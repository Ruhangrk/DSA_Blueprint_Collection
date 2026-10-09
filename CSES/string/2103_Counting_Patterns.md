# [2103 — Counting Patterns](https://cses.fi/problemset/task/2103)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** Aho-Corasick, multi-pattern matching  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string and patterns, count for each pattern the number of positions where it appears in the string.

### Input

The first input line has a string of length `n`.

The next input line has an integer `k`: the number of patterns. Finally, there are `k` lines that describe the patterns.

The string and the patterns consist of characters `a`–`z`.

### Output

For each pattern, print the number of positions.

### Constraints

- `1 <= n <= 1e5`
- `1 <= k <= 5e5`
- the total length of the patterns is at most `5e5`

### Examples

**Input**
```
aybabtu
3
bab
abc
a
```

**Output**
```
1
0
2
```

---

## What this problem really is

Same multi-pattern setting as existence checks, but report **occurrence counts** (overlapping starts allowed) for each pattern in text `S`.

## What to use

| Tool | Why |
|------|-----|
| Aho-Corasick | One pass finds all pattern hits |
| Visit counters on automaton nodes | Each time the text lands on a node, that end-position matches all patterns linked through fail |
| Fail-tree aggregation | Push counts from nodes to pattern terminals in `O(#nodes)` after the scan |
| Suffix automaton of `S` | Alternative: endpos-size of the state reached by the pattern |

## Approach

**Root idea:** every time the Aho-Corasick state after reading `S[0..i]` is node `v`, every pattern that is a suffix of `S[0..i]` ends at `i`. Those patterns sit on the fail-chain of `v`. Instead of walking the chain each step, add `1` to `v`’s visit counter, then after the scan push counts down the fail tree so each pattern node gets the total times it (or a descendant via fail) was hit.

**Mental picture:** drop a chip on the node you stand on after each character; at the end, chips slide along failure arrows to every dictionary word that just finished.

**Steps**

1. Build Aho-Corasick on all patterns; remember each pattern’s terminal node.
2. Scan `S`, increment `cnt[state]` for the state after each character.
3. Propagate: for nodes in decreasing depth / fail-tree DFS postorder, `cnt[fail[u]] += cnt[u]` (direction depends on whether you store parent-fail edges as a tree — usually add child visits into fail-parent when processing longer nodes first).
4. For each pattern, print `cnt[terminal]`.

**SAM alternative:** build SAM of `S`, propagate `endpos` sizes via suffix links; walking a pattern to state `v` gives occurrence count `endpos(v)`.

## Walkthrough (on a sample)

`S = aybabtu`: `bab` once, `abc` zero, `a` at two starts → `1 0 2`.

## Pitfalls

- Counting distinct patterns only (existence) instead of positions.
- Forgetting overlaps (`aaa` vs `aa` → 2).
- Quadratic fail-chain walking per character.
- Propagating counts in the wrong fail-tree order.

## Complexity

- Time: `O(|S| + total pattern length)`  
- Space: `O(total pattern length)`

## Key takeaway

**Pattern occurrence counts = Aho-Corasick visit counts aggregated on the failure tree** (or SAM `endpos` sizes).
