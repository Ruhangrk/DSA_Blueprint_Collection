# [2104 — Pattern Positions](https://cses.fi/problemset/task/2104)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** Aho-Corasick, multi-pattern matching  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string and patterns, find for each pattern the first position (1-indexed) where it appears in the string.

### Input

The first input line has a string of length `n`.

The next input line has an integer `k`: the number of patterns. Finally, there are `k` lines that describe the patterns.

The string and the patterns consist of characters `a`–`z`.

### Output

Print the first position for each pattern (or `-1` if it does not appear at all).

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
3
-1
1
```

---

## What this problem really is

Multi-pattern search again: for each pattern report the **leftmost starting index** (1-based) in text `S`, or `-1`.

## What to use

| Tool | Why |
|------|-----|
| Aho-Corasick | Find all hits in one left-to-right scan — first hit is the leftmost |
| Record end index on first terminal visit | `start = end - len + 1` |
| Output / fail links | Catch patterns that end as suffixes of the current match |
| SAM of `S` with `firstpos` | Alternative: first end-position of the state reached by the pattern |

## Approach

**Root idea:** scan `S` from left to right with Aho-Corasick. The first time a pattern’s terminal (or an output-link terminal) is reached at text index `i` (0-based end), the start is `i - length + 2` in 1-based indexing. Never overwrite an earlier answer.

**Mental picture:** same dictionary robot as multi-pattern matching; the first time it shouts a word’s name, stamp that word’s start position and ignore later shouts for it.

**Steps**

1. Build the automaton; store pattern length and id at terminals (several patterns may share a node).
2. Initialize `ans[id] = -1`.
3. Walk `S`; at end position `i` (0-based), follow output chain from the current state; for each unmarked pattern id, set `ans[id] = i - len + 2`.
4. Print all `ans`.

**SAM alternative:** each state stores the minimum endpos; after walking the pattern, `firstpos - m + 2` (careful with clone states’ inherited firstpos).

## Walkthrough (on a sample)

`aybabtu`: `bab` starts at `3`, `abc` absent → `-1`, `a` starts at `1`.

## Pitfalls

- Reporting the end index instead of the start.
- 0-based vs 1-based off-by-one (`+2` vs `+1`).
- Overwriting with a later occurrence.
- Missing patterns found only via failure/output links (suffix of another match).

## Complexity

- Time: `O(|S| + total pattern length)` with proper output links  
- Space: `O(total pattern length)`

## Key takeaway

**Leftmost pattern starts = first time Aho-Corasick hits each terminal while scanning left to right** — convert end index to start, keep the minimum.
