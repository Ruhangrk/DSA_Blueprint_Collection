# [1092 — Two Sets](https://cses.fi/problemset/task/1092)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** construction, math, greedy  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to divide the numbers `1, 2, ..., n` into two sets of equal sum.

### Input

The only input line contains an integer `n`.

### Output

Print `"YES"`, if the division is possible, and `"NO"` otherwise.

After this, if the division is possible, print an example of how to create the sets. First, print the number of elements in the first set followed by the elements themselves in a separate line, and then, print the second set in a similar way.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Example 1**

**Input**
```
7
```

**Output**
```
YES
4
1 2 4 7
3
3 5 6
```

**Example 2**

**Input**
```
6
```

**Output**
```
NO
```

---

## What this problem really is

Partition `{1..n}` into two subsets with equal sum (one may be printed first arbitrarily). Decide possibility and construct.

## What to use

| Tool | Why |
|------|-----|
| Total sum `S = n*(n+1)/2` | Need `S` even; each set sums to `S/2` |
| Mod 4 condition | `S` even iff `n % 4 == 0` or `n % 4 == 3` |
| Greedy fill | Take largest unused numbers into set A until target hit |

## Approach

**Root idea:** if `S` is odd, impossible. If even, fill one set to sum `S/2` by always taking the largest remaining integer that doesn’t overshoot (or simply walk `n..1` and take when it fits).

**Mental picture:** split a triangular pile of coins into two equal heaps; only certain `n` make the pile even; then greedily grab big coins for one heap.

**Steps**

1. `S = n*(n+1)/2` (`long long`). If `S` odd → `NO`.
2. Else `YES`. Target `T = S/2`.
3. For `v = n..1`: if `v <= T`, put `v` in set A and `T -= v`; else put in set B.
4. Print sizes and elements.

(Because every integer is considered once and `1` can always finish an exact fill when a partition exists, this works.)

## Walkthrough (on a sample)

`n=7`, `S=28`, `T=14`.

Take `7` (`T=7`), skip `6,5` (too big), take `4` (`T=3`), skip `3`, take `2,1`. Set A `{7,4,2,1}`, B `{6,5,3}` — matches sample idea.

`n=6`, `S=21` odd → **NO**.

## Pitfalls

- Only printing YES/NO without the sets.
- Using DP bitset for construction at `n=1e6` — unnecessary and heavy.
- `int` for `S`.

## Complexity

- Time: `O(n)`  
- Space: `O(n)` for output sets

## Key takeaway

**Equal-sum split of `1..n` exists iff `n % 4` is `0` or `3`** — then greedy-take from `n` down to fill sum `S/2`.
