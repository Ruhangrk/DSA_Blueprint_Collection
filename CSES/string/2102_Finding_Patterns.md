# [2102 — Finding Patterns](https://cses.fi/problemset/task/2102)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** Aho-Corasick, multi-pattern matching  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string and patterns, check for each pattern if it appears in the string.

### Input

The first input line has a string of length `n`.

The next input line has an integer `k`: the number of patterns. Finally, there are `k` lines that describe the patterns.

The string and the patterns consist of characters `a`–`z`.

### Output

For each pattern, print `"YES"` if it appears in the string and `"NO"` otherwise.

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
ayba
```

**Output**
```
YES
NO
YES
```

---

## What this problem really is

Multi-pattern substring existence: for each of many patterns, does it occur in text `S`? Total pattern length and `|S|` are large — one KMP per pattern is too slow in the worst case.

## What to use

| Tool | Why |
|------|-----|
| Aho-Corasick automaton | Search all patterns in one scan of `S` |
| Trie of patterns | Shared prefixes |
| Failure links | KMP-style fallback on mismatch / output links |
| Mark visited pattern terminals | Existence only |

## Approach

**Root idea:** put every pattern in one trie. Failure links jump to the longest proper suffix that is still a trie prefix. Scanning `S` walks this automaton; whenever you land on (or fail-link through) a node that ends a pattern, that pattern occurs.

**Mental picture:** a dictionary robot reading the text; on a wrong letter it doesn’t restart from scratch — it falls back to the best smaller dictionary prefix, like KMP but for many words at once.

**Steps**

1. Insert all patterns into a trie; store pattern id(s) at terminal nodes.
2. BFS to build failure links (and optionally output links to the next terminal on the fail chain).
3. Walk `S` from the root; follow goto / fail.
4. For each state reached, follow output/fail chain and mark those patterns found (do this carefully so each pattern is marked once — e.g. clear terminals after first hit, or use a visited flag on nodes).
5. Print `YES`/`NO` per pattern id.

**Alternative:** build a suffix automaton (or suffix array) of `S` and test each pattern by walking it — also `O(|S| + total pattern length)`.

## Walkthrough (on a sample)

`S = aybabtu`: `bab` occurs, `abc` does not, `ayba` occurs → `YES / NO / YES`.

## Pitfalls

- Walking the entire fail chain naively at every step without memoization — can be quadratic; use output links or batch marks.
- Duplicate patterns / multiple ids on one node.
- Running separate KMP for each pattern when `k` and lengths are huge.

## Complexity

- Time: `O(|S| + total pattern length + alphabet work)`  
- Space: `O(total pattern length)`

## Key takeaway

**Many “does pattern occur?” queries → one Aho-Corasick scan of the text** (or walk patterns on a SAM of the text).
