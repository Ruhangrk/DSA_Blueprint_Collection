# [2072 — Cut and Paste](https://cses.fi/problemset/task/2072)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** treap, implicit key, rope  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string, your task is to process operations where you cut a substring and paste it to the end of the string. What is the final string after all the operations?

### Input

The first input line has two integers `n` and `m`: the length of the string and the number of operations. The characters of the string are numbered `1,2,...,n`.

The next line has a string of length `n` that consists of characters `A`–`Z`.

Finally, there are `m` lines that describe the operations. Each line has two integers `a` and `b`: you cut a substring from position `a` to position `b`.

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
3 5
3 5
```

**Output**
```
AYABTUB
```

---

## What this problem really is

Repeated “cut `[a..b]` and append to the end” on a length-`n` string — need `O(log n)` per op, not `O(n)`.

## What to use

| Tool | Why |
|------|-----|
| Implicit treap / splay | Split/merge by position in `O(log n)` |
| Size field | Position = size of left subtree + 1 |
| Inorder print | Final string at the end |

## Approach

**Root idea:** represent the string as a balanced BST ordered by position. Cutting `[a..b]` to the end is three splits and two merges.

**Mental picture:** the string is a chain of beads in a treap. To cut a middle segment, snap the chain into left / mid / right, then glue left+right and hang mid at the tail.

**How that becomes the method**

1. Build an implicit treap with one node per character (random priorities, `sz` subtree sizes).
2. For each `(a,b)`:
   - `split(root, a-1)` → `L | Rest`
   - `split(Rest, b-a+1)` → `Mid | R`
   - `merge(L, R)` → `Without`
   - `merge(Without, Mid)` → new root
3. Inorder traversal prints the answer.

**Data structures:** treap node `{char val, priority, sz, left, right}`.

## Walkthrough (on a sample)

`AYBABTU`, cut `[3..5]` = `BAB` → `AY`+`TU`+`BAB` = `AYTUBAB`. Second cut `[3..5]` on that string moves another block to the end → `AYABTUB`.

## Pitfalls

- 0-based vs 1-based split sizes.  
- Forgetting to update `sz` after split/merge.  
- `O(n)` string erase/append per op → TLE.

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n)`

## Key takeaway

**Cut-and-paste substring to end = implicit treap split/merge** by position.
