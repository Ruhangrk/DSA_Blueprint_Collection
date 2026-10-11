# [2425 — Stack Weights](https://cses.fi/problemset/task/2425)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Inequalities, prefix/suffix sums, segment tree with range add and global min/max, reasoning about unknown weights  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` coins with distinct weights. Coin `i` is always heavier than coin `i-1`, but the exact weights are unknown.

Two stacks start empty. On each step, move one coin to a stack; coins are never removed. After each move, print:
- `<` if the right stack is certainly heavier,
- `>` if the left stack is certainly heavier,
- `?` if it cannot be determined from the information given.

### Input

- The first line has `n`.
- The next `n` lines each have `c` and `s`: move coin `c` to stack `s` (1 = left, 2 = right).

### Output

Print one line per move with `<`, `>`, or `?`.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
3
2 1
3 2
1 1
```

**Output**
```
>
<
?
```

**Explanation:** After the last move, if the coins are `[2,3,4]`, the left stack is heavier. If they are `[1,2,5]`, the right stack is heavier.

---

## What this problem really is

Each coin has an unknown positive weight, and the weights are strictly increasing in the coin index. After each move, we know the sign of `D = (sum of left weights) - (sum of right weights)` for every valid weight sequence. Output `>` if `D > 0` for all, `<` if `D < 0` for all, and `?` otherwise.

## What to use

| Tool | Why |
|------|-----|
| Telescoping of the weights | Writes every weight as a sum of positive increments, so `D` becomes a positive combination of simple quantities |
| Suffix sums `T[j]` of signs | These are the only coefficients that matter |
| Segment tree with range add and global min/max | Each move changes `T[j]` on a prefix `j <= c`; we need min and max of all `T[j]` after each move |

## Approach

**Root idea:** let `w[1] > 0` and `w[i] = w[i-1] + d[i]` with `d[i] > 0`. Let `s[i] = +1` if coin `i` is on the left, `-1` on the right, `0` if not placed. Then

`D = sum over i of s[i] * w[i] = sum over j of d[j] * T[j]`, where `T[j] = sum over i >= j of s[i]`.

Since each `d[j] > 0` can be any positive value independently, `D` has a fixed sign for every valid weight sequence exactly when all `T[j]` share a sign:
- all `T[j] >= 0` and not all zero → `D > 0` always → `>`,
- all `T[j] <= 0` and not all zero → `D < 0` always → `<`,
- otherwise (both signs occur) → `?`.

**Mental picture:** imagine `n` steps of a staircase. Each coin adds a "step" of positive height `d[j]`, and `T[j]` is how many more coins on the left than on the right sit at or above step `j`. The difference `D` is a weighted sum of these `T[j]` values, with all weights positive. If every step contributes in the same direction, `D` has that direction no matter how tall the steps are. If one step pushes left and another pushes right, the heights decide the winner, and the answer is unknown.

**How that becomes the method**

1. Keep a segment tree over `j = 1..n` storing `T[j]`, initially 0. Support range add and global min/max.
2. For a move of coin `c` to stack `s`, let `v = +1` (left) or `-1` (right). Add `v` to `T[j]` for all `j <= c`. This is because `s[c] = v` contributes to `T[j]` for every `j <= c`.
3. Let `mn` and `mx` be the global min and max of the tree.
4. Output:
   - `>` if `mn >= 0` (and not all zero, which cannot happen after a move),
   - `<` if `mx <= 0`,
   - `?` otherwise.

Note: a coin moved to the same stack twice is not possible, since each coin moves once. So every coin contributes exactly one `v`.

**Data structures:**
- Segment tree on `1..n` with lazy range add, storing min and max per node.

## Walkthrough (on a sample)

`n = 3`. Tree of `T[1..3]` starts `[0, 0, 0]`.

**Move 1: coin 2 to left (`v = +1`).** Add `+1` to `T[j]` for `j <= 2`: `T = [1, 1, 0]`. `mn = 0`, `mx = 1`. `mn >= 0` → `>`. ✓

**Move 2: coin 3 to right (`v = -1`).** Add `-1` to `T[j]` for `j <= 3`: `T = [0, 0, -1]`. `mn = -1`, `mx = 0`. `mx <= 0` → `<`. ✓

**Move 3: coin 1 to left (`v = +1`).** Add `+1` to `T[j]` for `j <= 1`: `T = [1, 0, -1]`. `mn = -1`, `mx = 1`. Both signs appear → `?`. ✓

Check the third step: `T = [1, 0, -1]` gives `D = d[1]*1 + d[2]*0 + d[3]*(-1) = d[1] - d[3]`. Coins `[2,3,4]` correspond to small `d[1]` relative to `d[3]` and make the left heavier, while coins `[1,2,5]` make the right heavier. Both are possible, so `?`.

## Pitfalls

- Indexing the range update incorrectly. A coin `c` affects `T[j]` for `j <= c`, not `j >= c`.
- Thinking about individual weights instead of increments. Working with `w[i]` directly does not give a clean sign rule.
- Forgetting that `w[1] > 0` is part of the model: it is the `d[1]` term. If the statement allowed `w[1]` to be arbitrary, the `d[1]` coefficient would not be strictly positive and the rule would change.
- Printing the symbols backwards. `>` means the left stack is heavier, `<` means the right stack is heavier.
- Using a naive `O(n)` scan of `T` per move, which is `O(n^2) = 4e10` for `n = 2e5`. The segment tree gives `O(n log n)`.

## Complexity

- Time: `O(n log n)`.
- Space: `O(n)`.

## Key takeaway

When unknown weights are increasing, write each weight as a base plus positive increments. The comparison then becomes a sign test on suffix sums of the placements, which a segment tree with range add tracks easily.
