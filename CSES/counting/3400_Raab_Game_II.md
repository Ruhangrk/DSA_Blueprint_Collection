# [3400 — Raab Game II](https://cses.fi/problemset/task/3400)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** combinatorics, DP, permutations  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a two player game where each player has `n` cards numbered `1,2,...,n`. On each turn both players place one of their cards on the table. The player who placed the higher card gets one point. If the cards are equal, neither player gets a point. The game continues until all cards have been played.

You are given the number of cards `n` and the players' scores at the end of the game, `a` and `b`. Your task is to count the number of possible games with that outcome.

### Input

The first line contains one integer `t`: the number of tests.

Then there are `t` lines, each with three integers `n`, `a` and `b`.

### Output

For each test case print the number of possible games modulo `1e9+7`.

### Constraints

- `1 <= t <= 1000`
- `1 <= n <= 5000`
- `0 <= a,b <= n`

### Examples

**Input**
```
5
3 1 2
2 0 1
5 2 2
9 3 5
4 4 1
```

**Output**
```
6
0
4200
976757050
0
```

---

## What this problem really is

Both players play a permutation of `1..n` (their card order). Ties when equal values in the same turn; otherwise higher wins a point. Count pairs of sequences (or equivalently: how player A’s cards align vs B’s) giving final scores `(a,b)`, mod `1e9+7`.

Usually model as: fix player A plays sorted `1..n` in some order — actually both choose permutations `p`, `q` of turns. Equivalent: pair each of A’s cards with B’s card in the same turn = a permutation π where A plays `i` against B’s `π(i)` if A’s order is identity… Cleaner: the multiset of pairs is a permutation matching. Number of games = `n! * n!` total; we count those with given win counts.

Standard model: A plays cards in order of a perm, B in order of a perm — relative pairing is one perm: values `1..n` of A vs permuted B. Score depends only on the matching between values. Then multiply by ways to order turns… Actually if both play all cards, a game is two permutations (turn orders). The pairs are `(p[t], q[t])` for `t=1..n`. Count such pairs of perms with `#\{t: p[t]>q[t]\}=a` and `#\{t: p[t]<q[t]\}=b` (and ties `n-a-b`).

## What to use

| Tool | Why |
|------|-----|
| Necessity `a+b <= n` | Ties take the rest |
| DP on cards used | Process values high to low |
| Factorials | Orderings of identical outcomes |

## Approach

**Root idea:** think of the `n` turns as a matching between A’s copies of `1..n` and B’s. Sort turns by the larger card to build DP: when processing number `x`, it can be played by A or B on a turn against a smaller leftover, determining who wins that turn.

**Mental picture:** all cards from both hands enter a timeline; each value appears twice (once per player). Pairing equal values causes a tie turn; unequal pairs give a point to the owner of the higher card.

**How that becomes the method**

1. If `a+b > n` → `0`. Let `z = n - a - b` (ties). Ties require pairing equal ranks — there are limited structures.  
2. Precompute factorial / invfact for multinomial coefficients of ordering turns.  
3. DP / combinatorial formula: choose which `z` values are tied (both play that value in the same turn — only one way per value to pair equals), then the remaining `a+b` values form a sequence of decisive games counted by choosing positions and a ballot-like matching so A wins `a` and B wins `b`.  
4. Multiply by `(n!)^2 / symmetries` carefully — or count matchings first then `n!` ways to order the pairs as turns.

A clean counting path: number of permutations `π` of `1..n` where we compare identity vs `π` is wrong. Two independent perms `p,q`: let `r = q^{-1} ∘ p` … 

Practical editorial-style DP: `dp[i][x][y]` = ways using cards `1..i` with scores `x,y` — too big for `n=5000`. Need `O(n^2)` precompute for all `(a,b)` then answer queries in `O(1)`.

Precompute for each `n<=5000` is heavy across tests — tests have varying `n`. Precompute globally largest N: `O(N^2)` DP for fixed max n.

**Data structures:** `dp` table; fact arrays; mod `1e9+7`.

## Walkthrough (on a sample)

`(3,1,2)` has 6 games; `(2,0,1)` impossible → `0`.

## Pitfalls

- Ignoring ties.  
- Double-counting turn orders.  
- `a+b>n` not returning 0.

## Complexity

- Time: `O(N^2)` precompute + `O(1)` per test (or `O(n^2)` per test if careful with sum n)  
- Space: `O(N^2)`

## Key takeaway

**Raab Game II = count pairs of play-orders with given win/tie profile** — combinatorics/DP on matchings, mod `1e9+7`.
