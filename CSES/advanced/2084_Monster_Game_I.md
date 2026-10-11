# [2084 — Monster Game I](https://cses.fi/problemset/task/2084)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** DP, convex hull trick, CHT  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are playing a game that consists of `n` levels. Each level has a monster. On levels `1,2,...,n-1`, you can either kill or escape the monster. However, on level `n` you must kill the final monster to win the game.

Killing a monster takes `s*f` time where `s` is the monster's strength and `f` is your skill factor (lower skill factor is better). After killing a monster, you get a new skill factor. What is the minimum total time in which you can win the game?

### Input

The first input line has two integers `n` and `x`: the number of levels and your initial skill factor.

The second line has `n` integers `s1, s2, ..., sn`: each monster's strength.

The third line has `n` integers `f1, f2, ..., fn`: your new skill factor after killing a monster.

### Output

Print one integer: the minimum total time to win the game.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x <= 1e6`
- `1 <= s1 <= s2 <= ... <= sn <= 1e6`
- `x >= f1 >= f2 >= ... >= fn >= 1`

### Examples

**Input**
```
5 100
20 30 30 50 90
90 60 20 20 10
```

**Output**
```
4800
```

Explanation: The best way to play is to kill the third and fifth monster.

---

## What this problem really is

Choose an increasing sequence of kills ending at `n`. Cost between consecutive kills `j → i` is `s[i] * f[j]` (skill after `j`). Initial skill `x` acts as a virtual kill `0`. Min total cost — classic DP sped up by CHT because `s` grows and `f` shrinks.

## What to use

| Tool | Why |
|------|-----|
| DP lines `dp[j] + f[j]*s[i]` | `dp[i] = min_j (dp[j] + s[i]*f[j])` |
| Convex hull trick (deque) | Monotone slopes `f` and monotone queries `s` |
| 64-bit | Products up to `~1e6 * 1e6 * n` scale in sums |

## Approach

**Root idea:** after the last kill before `i`, your skill is fixed, so the cost to kill `i` is linear in that skill. Optimal previous kills form lines; you query the lower envelope at `x = s[i]`.

**Mental picture:** each kill you take installs a new “price tag” (skill). Later monsters charge `strength × current price`. You want the cheapest total bill ending with a mandatory purchase of monster `n`.

**How that becomes the method**

1. Index monsters `1..n`. Let `f[0] = x`, `dp[0] = 0` (start).
2. Process `i = 1..n`:
   - `dp[i] = min over available lines j: dp[j] + s[i] * f[j]` (only previous kills, including `0`).
   - Insert line `(slope = f[i], intercept = dp[i])` for future use.
3. Answer `dp[n]`.

Because `f` is non-increasing and `s` non-decreasing, maintain the lower hull in a deque: pop back while last intersection is dominated; pop front while next line is better for current `s[i]`.

**Data structures:** deque of lines `(m, b)` with eval `m*x+b`; `dp[0..n]`.

## Walkthrough (on a sample)

Killing only 3 then 5: pay `30*100 + 90*20 = 3000+1800 = 4800`. Other kill sets cost at least that.

## Pitfalls

- Forcing a kill on every level.  
- Using `f[i]` as the skill *during* the kill of `i` (skill comes from the **previous** kill).  
- Float intersections — prefer integer cross checks.  
- `O(n^2)` DP — TLE.

## Complexity

- Time: `O(n)` with monotone CHT (after `O(n)` setup)  
- Space: `O(n)`

## Key takeaway

**Monster Game I = DP `min (dp[j] + s[i]*f[j])` + CHT** under monotone `s` and `f`.
