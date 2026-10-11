> **Warning: this solution might be incorrect.** I have no verified solution for this problem. The page records the statement only.

# [2427 — Letter Pair Move Game](https://cses.fi/problemset/task/2427)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Status:** Unsolved (no verified solution)  

---

## Problem Statement

There are `2n` boxes. Two adjacent boxes are empty, and the rest hold `A` or `B`, with `n-1` of each. A move picks two adjacent boxes holding letters and moves them into the two adjacent empty boxes, keeping their order. Make all `A` come before all `B`, in at most `1000` moves. Print the moves, or `-1` if impossible.

### Input

- The first line has `n`.
- The second line has the `2n` characters.

### Output

`k` then `k` lines of board states, or `-1`.

### Constraints

- `1 <= n <= 100`

### Examples

**Input**
```
3
AB..BA
```

**Output**
```
2
ABBA..
A..ABB
```

**Input**
```
3
ABAB..
```

**Output**
```
-1
```

---

## What I have

Nothing verified. I checked the sample moves: `AB..BA -> ABBA.. -> A..ABB`, and each move moves a pair of adjacent letters into the empty pair.

## Key takeaway

No solution yet.
