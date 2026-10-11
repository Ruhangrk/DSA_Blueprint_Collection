# [3422 — Third Permutation](https://cses.fi/problemset/task/3422)

**Platform:** CSES  
**Source:** CSES Problem Set — Construction Problems  
**Tags:** construction, permutations, derangement  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given two permutations `a` and `b` such that `a[i] != b[i]` in every position. Create a third permutation `c` such that `a[i] != c[i]` and `b[i] != c[i]` in every position.

### Input

The first line has an integer `n`: the permutation size.

The second line has `n` integers `a1, a2, ..., an`.

The third line has `n` integers `b1, b2, ..., bn`.

### Output

Print `n` integers `c1, c2, ..., cn`. You can print any valid solution. If there are no solutions, print `IMPOSSIBLE`.

### Constraints

- `2 <= n <= 1e5`

### Examples

**Input**
```
5
1 3 2 5 4
4 1 3 2 5
```

**Output**
```
3 2 5 4 1
```

---

## What this problem really is

Find a permutation `c` that is a derangement relative to both `a` and `b` positionwise (`c[i]` avoids two forbidden values at each `i`), using each of `1..n` once.

## What to use

| Tool | Why |
|------|-----|
| Graph matching / cycles | Position `i` must get a value not in `{a[i],b[i]}` |
| Greedy with swaps | Try `c[i] = i` shifted / compose permutations |
| Composition idea | Think in value space: map positions through `a`,`b` |

## Approach

**Root idea:** at each index two values are banned. For `n>=2` with the given promise `a[i]!=b[i]`, a third latin “row” usually exists — construct by taking a cyclic shift of identity in the right labeling, or by building a graph where values point to allowed positions and finding a perfect matching.

**Mental picture:** each seat rejects two name tags; seat everyone so no one gets a rejected tag and all tags are unique.

**How that becomes the method**

1. Relabel so you work with “which value sits where.” One reliable construction: set `c[i] = p[a[i]]` for a carefully chosen derangement `p` of values so that also `p[a[i]] != b[i]` for all `i` — i.e. `p` avoids the matching induced by `a^{-1} compose b`.
2. Practical greedy: put positions in a list; assign to each position any unused value outside `{a[i],b[i]}`; if stuck, swap with an earlier position that can trade. With `n>=3` this always works; for `n=2` check exhaustively (only two perms — may be `IMPOSSIBLE` if both equal `{a,b}` patterns).
3. For `n=2`: only perms are `[1,2]` and `[2,1]`; test both against the constraints.

**Data structures:** arrays `a,b,c`; boolean `used[n+1]`; optional position lists per value.

## Walkthrough (on a sample)

`c = 3 2 5 4 1` differs from both `a` and `b` everywhere and is a permutation.

## Pitfalls

- Ignoring `n=2` impossible cases.  
- Producing a sequence that is not a permutation.  
- Only avoiding `a` but colliding with `b`.

## Complexity

- Time: `O(n)` greedy / matching  
- Space: `O(n)`

## Key takeaway

**Third permutation avoiding two given ones = derangement vs two banned values per index** — greedy with repair, watch `n=2`.
