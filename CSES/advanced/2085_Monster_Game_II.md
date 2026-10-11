# [2085 — Monster Game II](https://cses.fi/problemset/task/2085)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** DP, Li Chao, convex hull trick  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are playing a game that consists of `n` levels. Each level has a monster. On levels `1,2,...,n-1`, you can either kill or escape the monster. However, on level `n` you must kill the final monster to win the game.

Killing a monster takes `s*f` time where `s` is the monster's strength and `f` is your skill factor. After killing a monster, you get a new skill factor (lower skill factor is better). What is the minimum total time in which you can win the game?

### Input

The first input line has two integers `n` and `x`: the number of levels and your initial skill factor.

The second line has `n` integers `s1, s2, ..., sn`: each monster's strength.

The third line has `n` integers `f1, f2, ..., fn`: your new skill factor after killing a monster.

### Output

Print one integer: the minimum total time to win the game.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x <= 1e6`
- `1 <= si, fi <= 1e6`

### Examples

**Input**
```
5 100
50 20 30 90 30
60 20 20 10 90
```

**Output**
```
2600
```

Explanation: The best way to play is to kill the second and fifth monster.

---

## What this problem really is

Same kill/escape DP as Monster Game I, but `s` and `f` are **not** monotone — so a plain deque CHT is unsafe; you need a structure that inserts arbitrary lines and queries arbitrary `x`.

## What to use

| Tool | Why |
|------|-----|
| Same recurrence | `dp[i] = min_j dp[j] + s[i]*f[j]` with `f[0]=x`, `dp[0]=0` |
| Li Chao tree on `s`-domain | Insert lines `(f[j], dp[j])`, query at `s[i]` |
| 64-bit | Large products / sums |

## Approach

**Root idea:** the recurrence does not change. Only the geometry of lines does: slopes `f[j]` and query abscissae `s[i]` arrive in arbitrary order, so maintain the lower envelope with Li Chao (or a general CHT multiset).

**Mental picture:** each past kill drops a pricing line `time = skill * s + past_cost`. New monsters ask “what’s the cheapest line at my strength?”; after you pay, you publish a new line with your new skill.

**How that becomes the method**

1. Li Chao over `x` in `[1..1e6]` (strength domain), minimizing `m*x + b`.
2. Insert initial line `m = x`, `b = 0`.
3. For `i = 1..n`:
   - `dp[i] = query(s[i])`
   - Insert line `m = f[i]`, `b = dp[i]`
4. Print `dp[n]`.

You only need lines from kills you actually took; escaping a level simply means not inserting / not using it as a previous `j` — which is already modeled by only inserting after you compute `dp[i]` for a kill at `i`. Wait: do we insert for every `i`, or only if we kill `i`?

Correct model: `dp[i]` = min cost of a sequence of kills that **ends with killing `i`**. We always “consider” killing `i` when computing `dp[i]`; for levels we might skip, we simply never use them as an ending state except that we still compute `dp[i]` for all `i` because any `i` could be a previous kill for a later one. Escaping `i` means not using `i` as a kill in the path — paths that skip `i` are represented by transitioning from some `j < i` directly to some `k > i`, which never needs a `dp` state at the escaped index. So we should still compute and insert `dp[i]` for every `i` (the cost if we *do* kill `i` as a last kill so far). Paths that skip `i` just will not choose that state. Good.

**Data structures:** Li Chao nodes storing best `(m, b)` for min; `dp[1..n]`.

## Walkthrough (on a sample)

Kill 2 then 5: `20*100 + 30*20 = 2000+600 = 2600`. Arbitrary order of `s`/`f` still handled by Li Chao queries/inserts.

## Pitfalls

- Reusing a monotone deque from Game I (wrong without sorted slopes/queries).  
- Skill used for killing `i` must be from the **previous** kill’s `f`, not `f[i]`.  
- Overflow.

## Complexity

- Time: `O(n log S)` with `S = 1e6`  
- Space: `O(n log S)` dynamic or `O(S)` static

## Key takeaway

**Monster Game II = same line DP as I, but Li Chao** — no monotonicity to lean on.
