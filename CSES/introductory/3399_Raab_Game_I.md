# [3399 — Raab Game I](https://cses.fi/problemset/task/3399)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** construction, permutations, games  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a two player game where each player has `n` cards numbered `1, 2, ..., n`. On each turn both players place one of their cards on the table. The player who placed the higher card gets one point. If the cards are equal, neither player gets a point. The game continues until all cards have been played.

You are given the number of cards `n` and the players' scores at the end of the game, `a` and `b`. Your task is to give an example of how the game could have played out.

### Input

The first line contains one integer `t`: the number of tests.

Then there are `t` lines, each with three integers `n`, `a` and `b`.

### Output

For each test case print `YES` if there is a game with the given outcome and `NO` otherwise.

If the answer is `YES`, print an example of one possible game. Print two lines representing the order in which the players place their cards. You can give any valid example.

### Constraints

- `1 <= t <= 1000`
- `1 <= n <= 100`
- `0 <= a, b <= n`

### Examples

**Input**
```
5
4 1 2
2 0 1
3 0 0
2 1 1
4 4 1
```

**Output**
```
YES
1 4 3 2
2 1 3 4
NO
YES
1 2 3
1 2 3
YES
1 2
2 1
NO
```

---

## What this problem really is

Both players use each of `1..n` **exactly once** (two permutations). Ties when equal. Reconstruct permutations with final scores `(a,b)`, or say impossible.

## What to use

| Tool | Why |
|------|-----|
| Counting | `a + b` decisive rounds; `n - a - b` ties |
| Same multiset invariant | One player cannot win all decisive tricks if the other has 0 wins (except all ties) |
| Cyclic shift construction | Build exact `(a,b)` on the non-tie suffix |

## Approach

**Root idea:** necessary and sufficient:

1. `a + b <= n`
2. Either `a = b = 0`, or both `a > 0` and `b > 0`

If one score is `0` and the other is positive, the zero-score player loses every non-tie. With identical card sets, the sums of cards played in decisive rounds match in a way that forbids a total shutout once any tie is mixed in — and a full shutout with no ties is also impossible (card sums equal). So one-sided positive scores never work.

**Mental picture:** two identical decks dealt in some order; you need both players to “steal” some tricks if any real fights happen.

**Construction when YES**

- If `a = b = 0`: both print `1 2 ... n` (all ties).
- Otherwise (`a,b > 0`, `a+b <= n`):
  1. First `t = n - a - b` positions: both play `1, 2, ..., t` (ties).
  2. Remaining values `t+1 .. n` (size `m = a+b`). Player A plays them in order `t+1, t+2, ..., n`. Player B plays the same list rotated left by `a` positions (cyclic). That yields exactly `a` wins for A and `b` for B.

**Steps**

1. Check the possibility rule; else `NO`.
2. Emit `YES` and the two sequences as above.

## Walkthrough (on a sample)

`(2,0,1)`: one positive score only → **NO**.

`(3,0,0)`: all ties → both `1 2 3`.

`(2,1,1)`: `t=0`, rotate by `1`: A=`1 2`, B=`2 1` → A wins once, B once.

`(4,4,1)`: `4+1 > 4` → **NO**.

## Pitfalls

- Only checking `a+b <= n` (misses cases like `2 0 1`).
- Allowing different multisets of cards — both must use `1..n`.
- Off-by-one in the rotation length `a`.

## Complexity

- Time: `O(t * n)`  
- Space: `O(n)`

## Key takeaway

**Raab scores are possible iff `a+b <= n` and scores are both zero or both positive** — build with leading ties plus a cyclic shift of the remaining cards.
