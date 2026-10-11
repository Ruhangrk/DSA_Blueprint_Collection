# [2073 — Substring Reversals](https://cses.fi/problemset/task/2073)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** treap, lazy reverse, implicit key  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string, your task is to process operations where you reverse a substring of the string. What is the final string after all the operations?

### Input

The first input line has two integers `n` and `m`: the length of the string and the number of operations. The characters of the string are numbered `1,2,...,n`.

The next line has a string of length `n` that consists of characters `A`–`Z`.

Finally, there are `m` lines that describe the operations. Each line has two integers `a` and `b`: you reverse a substring from position `a` to position `b`.

### Output

Print the final string after all the operations.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= a <= b <= n`

### Examples

**Input**
```
7 2
AYBABTU
3 4
4 7
```

**Output**
```
AYAUTBB
```

---

## What this problem really is

Many range reverses on a string — again need logarithmic sequence updates, now with a **lazy reverse** flag.

## What to use

| Tool | Why |
|------|-----|
| Implicit treap | Split out `[a..b]` |
| Lazy `rev` bit | Reverse = flip children order; push before split/access |
| Merge back | Put the reversed piece back in place |

## Approach

**Root idea:** reversing a contiguous position range means isolating that segment’s treap, toggling a reverse lazy tag on its root, and merging back. The tag swaps left/right when pushed.

**Mental picture:** the string lives in a treap. To reverse a window, box that window, stamp “flip me later” on the box, and put the box back. The flip is applied only when you next open the box.

**How that becomes the method**

1. Build implicit treap over the string; each node has `rev` lazy (initially false).
2. `push(v)`: if `rev`, swap `left/right`, xor `rev` onto children, clear `v->rev`.
3. Always `push` before reading sizes / descending for split.
4. Operation `(a,b)`: split to `L | Mid | R`, set `Mid->rev ^= 1`, merge `L+Mid+R`.
5. Final inorder (with pushes) prints the string.

**Data structures:** treap node `{char, priority, sz, rev, left, right}`.

## Walkthrough (on a sample)

`AYBABTU` → reverse `[3..4]` (`BA`→`AB`) → `AYABB TU`… then reverse `[4..7]` yields `AYAUTBB`.

## Pitfalls

- Splitting without pushing lazy.  
- Not propagating `rev` to children when pushing.  
- Confusing this with cut-and-paste (here the piece stays in the same position, only flipped).

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n)`

## Key takeaway

**Range reverse on a string = implicit treap + lazy reverse tag.**
