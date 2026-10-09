# [1631 — Reading Books](https://cses.fi/problemset/task/1631)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** greedy, sorting  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` books, and Kotivalo and Justiina are going to read them all. For each book, you know the time it takes to read it.

They both read each book from beginning to end, and they cannot read a book at the same time. What is the minimum total time required?

### Input

The first input line has an integer `n`: the number of books.

The second line has `n` integers `t1, t2, ..., tn`: the time required to read each book.

### Output

Print one integer: the minimum total time.

### Constraints

- `1 <= n <= 2e5`
- `1 <= ti <= 1e9`

### Examples

**Input**
```
3
2 8 3
```

**Output**
```
16
```

---

## What this problem really is

Two people must each read every book; a book cannot be read by both at once. Minimize the wall-clock time until both have finished everything.

## What to use

| Tool | Why |
|------|-----|
| `S = sum(t)` | Total work is `2*S` reader-time; with 2 readers wall time `>= S` |
| `M = max(t)` | Longest book read twice with no overlap → wall time `>= 2*M` |
| Closed form | Answer is exactly `max(S, 2*M)` |

## Approach

**Root idea:** two lower bounds are tight:

- Work `2*S` split across two readers ⇒ at least time `S`.
- The longest book must be read by both people **without overlapping** ⇒ at least time `2*M`.

So the minimum time is `max(S, 2*M)`.

**Mental picture:** two passes over the shelf. If one book is huge, each reader’s turn on it is serial — you pay `2*M`. If books are balanced, both stay busy and finish at time `S`.

**Steps**

1. Compute `S = sum(t)` and `M = max(t)`.
2. Print `max(S, 2 * M)`.

## Walkthrough (on a sample)

Times `2,8,3`. `S=13`, `M=8`. `2*M=16 > 13` → **16**.

One reader takes the book of `8` first; the other finishes `2+3=5` and waits; then they swap on the long book — total `8+8=16`.

## Pitfalls

- Answering `2*S` (no parallelism).
- Answering only `S` when one book is longer than the sum of the rest (`2*M` dominates).
- `int` overflow on `S` (`n * 1e9`).

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Two readers, every book twice, no shared book at once → time = `max(sum, 2 * max)`**.
