# [1097 — Removal Game](https://cses.fi/problemset/task/1097)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, interval DP, game theory  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a list of `n` numbers and two players who move alternately. On each move, a player removes either the first or last number from the list, and their score increases by that number. Both players try to maximize their scores.

What is the maximum possible score for the first player when both players play optimally?

### Input

The first input line contains an integer `n`: the size of the list.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the list.

### Output

Print the maximum possible score for the first player.

### Constraints

- `1 <= n <= 5000`
- `-1e9 <= xi <= 1e9`

### Examples

**Input**
```
4
4 5 1 3
```

**Output**
```
8
```

---

## What this problem really is

Two players optimally play on a subarray, always taking one of the two ends. Maximize **first player’s total points**. Values can be negative; `n` up to `5000`.

## What to use

| Tool | Why |
|------|-----|
| Interval DP `dp[l][r]` | Game on subarray `x[l..r]` only depends on that segment |
| Prefix sums | Fast range sum so you can convert “opponent’s score” into “your score” |
| `long long` | Values and sums up to about `5000 * 1e9` |

## Approach

**Root idea:** on subarray `l..r`, the player about to move wants to maximize **their own** total from this whole subgame. They choose left or right end; then the opponent, playing optimally on what’s left, gets `dp[next]` points from that smaller game. Your points = (sum of this subarray) − (opponent’s points).

**Mental picture:** the array is a row of coins. Each turn you steal the leftmost or rightmost remaining coin. DP answers: “if only this segment were left and it were my turn, how many points do I finish with?”

**Data structures**

- Array `x[0..n-1]` (or 1-based).
- Prefix sums `pref` so `sum(l,r)` is `O(1)`.
- `dp[l][r]` = max score for the **current** player on `x[l..r]`.

**Recurrence**

```
sum(l,r) = total of x[l..r]

dp[l][l] = x[l]

dp[l][r] = max(
  x[l] + sum(l+1, r) - dp[l+1][r],   // take left; opponent gets dp[l+1][r] on the rest
  x[r] + sum(l, r-1) - dp[l][r-1]    // take right
)
```

Because after you take `x[l]`, opponent becomes the “current player” on `l+1..r` and scores `dp[l+1][r]`; you later collect everything else in that segment, i.e. `sum(l+1,r) - dp[l+1][r]`, plus the `x[l]` you already took.

**Steps**

1. Build prefix sums.
2. Fill `dp` by increasing interval length.
3. Answer is `dp[0][n-1]` (first player on the full array).

## Walkthrough (on a sample)

`x = [4, 5, 1, 3]`, total `13`.

First player can get **`8`** with optimal play (sample). One optimal line of play:

- Take `3` (right) → remain `[4,5,1]`
- Opponent plays optimally on that triple…
- First finishes with `8`, second with `5`.

DP computes the same number bottom-up from length-1 intervals up to length 4 without enumerating the full game tree by hand.

## Pitfalls

- **Both maximize their own score** — not “minimize opponent” as a separate goal; the sum-complement formula already encodes that.
- **Negative numbers** — still use the same recurrence; do not assume taking the larger end is greedy-optimal.
- **`n = 5000`** → `O(n^2)` time/space is the limit; `O(n^3)` TLEs.
- Use `long long` for `dp` and sums.

## Complexity

- Time: `O(n^2)`  
- Space: `O(n^2)` (or `O(n)` with care, but `n=5000` square usually OK)

## Key takeaway

**Optimal end-taking game → interval DP.** Current player score = max over ends of `taken + (segment sum after take − opponent’s dp on remainder)`.
