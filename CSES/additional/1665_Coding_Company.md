# [1665 — Coding Company](https://cses.fi/problemset/task/1665)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Sorting, DP over sorted values, counting set partitions, open-team state  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` coders with skill levels `t[1..n]`, each between `0` and `100`. Split the coders into teams. The penalty of a team is its maximum skill minus its minimum skill. Count the ways to split all coders into teams such that the sum of penalties is at most `x`, modulo `1e9+7`.

### Input

- The first line has `n` and `x`.
- The second line has `t[1..n]`.

### Output

Print the count modulo `1e9+7`.

### Constraints

- `1 <= n <= 100`
- `0 <= x <= 5000`
- `0 <= t[i] <= 100`

### Examples

**Input**
```
3 2
2 5 3
```

**Output**
```
3
```

---

## What this problem really is

A team's penalty depends only on its smallest and largest coder. So a team is described by two "end" coders, and every other member just sits somewhere in between. If we sort the coders by skill and walk through them in order, each team is:

- **opened** at its lowest coder, which adds `-t` to the penalty running sum,
- **closed** at its highest coder, which adds `+t`,
- with optional middle coders in between.

The total penalty is the sum over teams of `(max - min)`, which is exactly the sum of all `+t` minus all `-t` in this walk.

## What to use

| Tool | Why |
|------|-----|
| Sort `t` ascending | Turns "min and max of a team" into "first and last member met" |
| State `(j, P)` | `j` = number of currently open teams, `P` = running penalty so far |
| Counting with multipliers | Joining an open team as a middle member has `j` choices |
| Prune `P > x` | Keeps the state space small |

## Approach

**Root idea:** process the coders from smallest to largest skill. For each coder, decide one of these four things:

1. **Open a new team** with this coder as its minimum. Number of open teams goes `j -> j+1`. Running penalty `P -= t`.
2. **Join an open team** as a middle member (not the first or last). Choose any of the `j` open teams, so multiply by `j`. State unchanged.
3. **Close an open team** with this coder as its maximum. Choose any of the `j` open teams, so multiply by `j`. Number of open teams goes `j -> j-1`. Running penalty `P += t`.
4. **Singleton team**: a team consisting only of this coder. Penalty `0`, state unchanged, multiply by `1`.

At the end we need `j = 0` (no team left open), and we sum the counts for all `P <= x`.

**Why the running penalty can be pruned at `x`:** `P` is the penalty of the teams that are already closed, minus the minimums of teams still open. Open teams only make `P` smaller, and closed teams never have negative penalty. So `P` never exceeds the final penalty, and any state with `P > x` cannot lead to a valid answer.

**Why `P` is bounded below:** `P >= -(sum of open minimums) >= -100 * n`, so the lower end of the range is small.

**Mental picture:** sweep a vertical line through the sorted coders. Open teams are like threads hanging in the air, each with a weight of `-t` on its lowest coder. When a thread is tied off at a higher coder, its net penalty `t_high - t_low` is locked in. Every coder either starts a thread, adds to a hanging thread, ties one off, or stands alone.

**Method**

1. Sort `t` ascending.
2. Start with `dp[0][0] = 1` (no open teams, penalty `0`).
3. For each coder in order, build the new table from the old one using the four moves above.
4. Answer is the sum of `dp[0][P]` for `0 <= P <= x`.

## Walkthrough (on the sample)

Sorted: `2 3 5`, `x = 2`. Notation: state `(j, P)` with count.

**After coder `2`:**

| State | Count | Meaning |
|-------|-------|---------|
| `(1, -2)` | 1 | team opened at 2 |
| `(0, 0)` | 1 | singleton `{2}` |

**After coder `3`:**

| State | Count | Source |
|-------|-------|--------|
| `(2, -5)` | 1 | opened new team at 3 from `(1,-2)` |
| `(1, -3)` | 1 | opened new team at 3 from `(0,0)` |
| `(1, -2)` | 2 | joined team opened at 2 (from `(1,-2)`), or singleton `{3}` while `{2}` is open |
| `(0, 1)` | 1 | closed team `{2,3}` at 3: `-2 + 3 = 1` |
| `(0, 0)` | 1 | singleton `{3}` from `(0,0)` |

**Coder `5` (final step), keep only `j = 0`:**

| From | Move | Count | New P | Partition |
|------|------|-------|-------|-----------|
| `(0, 0)` | singleton `{5}` | 1 | 0 | `{2}, {3}, {5}` |
| `(0, 1)` | singleton `{5}` | 1 | 1 | `{2,3}, {5}` |
| `(1, -3)` | close `{3,5}` | 1 | 2 | `{2}, {3,5}` |
| `(1, -2)` | close `{2,5}` or `{2,3,5}` | 2 | 3 | penalty 3 > 2, pruned |

Valid totals with `P <= 2`: `1 + 1 + 1 = 3`. Matches the expected output.

The partition `{2,3,5}` (one team) has penalty `3`, so it is correctly excluded.

## Pitfalls

- Not sorting. The open/close idea needs the coders in increasing skill order.
- Forgetting the singleton move. A team of one coder has penalty `0` and must be counted.
- Counting joins without the multiplier `j`. Any open team can receive a middle member.
- Ending with open teams. Only `j = 0` at the end is valid.
- Using `int` for counts. Reduce modulo `1e9+7` at each addition.
- Negative indices for `P`. Store `P` with an offset.

## Complexity

- States: `j` up to `n`, `P` between `-100 * n` and `x`.
- Time: about `O(n * n * (x + 100 * n))`, roughly `1e8` simple operations for the limits.
- Space: `O(n * (x + 100 * n))` with rolling arrays over coders.

## Key takeaway

Sort the values and sweep. A team's penalty is "closing skill minus opening skill", so a sweep that tracks the number of open teams and the running penalty counts all partitions at once. This open/close sweep is a standard way to count partitions where each group's cost depends only on its extremes.
