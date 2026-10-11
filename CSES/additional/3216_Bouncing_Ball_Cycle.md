# [3216 — Bouncing Ball Cycle](https://cses.fi/problemset/task/3216)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Periodic motion, lcm and gcd, residue counting, inclusion-exclusion  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

The same bouncing ball as in the previous problem: it starts at the top-left corner of an `n x m` grid, moving diagonally, and changes direction on each border hit. Find:

1. The number of steps until it returns to its starting cell.
2. The number of distinct cells it visits.

### Input

- The first line has `t`.
- Each of the next `t` lines has `n` and `m`.

### Output

For each test, print the steps and the number of distinct visited cells.

### Constraints

- `1 <= t <= 1000`
- `2 <= n, m <= 1e9`

### Examples

**Input**
```
4
3 4
2 2
19 18
42 1337
```

**Output**
```
12 6
2 2
612 171
109552 28077
```

---

## What this problem really is

Each axis repeats with period `2(n-1)` for rows and `2(m-1)` for columns. The ball returns to its start when both axes return to their start at the same time. The distinct cell count is a question about which pairs of row-phase and column-phase can happen together.

## What to use

| Tool | Why |
|------|-----|
| `lcm` of the two periods | The first time both axes are back at the start |
| Row phase and column phase | Each cell is a pair of phases, so counting cells means counting compatible pairs |
| `gcd` condition | Two phases can occur together only if they agree modulo `2 * gcd(n-1, m-1)` |
| Floor-division counting | Counts how many phases fall in each residue class, in `O(1)` |

## Approach

**Root idea (steps):** the row returns to row `1` moving down every `2(n-1)` steps, and the column every `2(m-1)` steps. Both must return at the same time, so the answer is `lcm(2(n-1), 2(m-1)) = 2 * lcm(n-1, m-1)`.

**Root idea (cells):** write `a = n-1`, `b = m-1`. Each row `r` is reached at phase `u = r-1` or `2a - u` on the cycle of length `2a`. Each column is similar with `v`. The ball is in cell `(r, c)` at some time `t` exactly when the row phase and the column phase both match `t`. Two phases `x` (mod `2a`) and `y` (mod `2b`) can be matched by the same time `t` if and only if `x ≡ y (mod 2g)`, where `g = gcd(a, b)`. So a cell `(r, c)` is visited iff `u ≡ v` or `u ≡ -v` modulo `2g`.

**Mental picture:** think of the rows and columns as two clocks that tick at different rates. Each cell is a pair of clock readings. The ball visits exactly the pairs of readings that some moment on the joint clock can produce. Two readings line up only when they agree modulo the shared tick `2g`. Counting the cells is counting the agreeing pairs, with the `u ≡ -v` case added and the double-counted case removed.

**How that becomes the method**

1. Steps: `2 * (a / g) * b`, computed as `2 * lcm(a, b)` in 64-bit.
2. Cells: let `M = 2g`, `qa = a / M`, `ra = a mod M`, `qb = b / M`, `rb = b mod M`.
   - `P1` = number of pairs with `u ≡ v (mod M)`.
   - `P2` = number of pairs with `u ≡ -v (mod M)`.
   - `P12` = pairs satisfying both, which happen when `u ≡ 0 (mod g)` and `u ≡ v (mod M)`.
   - Answer = `P1 + P2 - P12`.
3. Each count is a sum over residues. Residues in `[0, M-1]` either contain `qa + 1` or `qa` values of `u` in `[0, a]`, depending on whether the residue is at most `ra`. Products of these counts are summed in closed form.

**Data structures:** only integers. Use `__int128` for intermediate products, since they can exceed 64-bit before the final result.

## Walkthrough (on a sample)

Take `n = 3`, `m = 4`. Then `a = 2`, `b = 3`, `g = 1`, `M = 2`.

**Steps:** `lcm(2, 3) = 6`, so the answer is `2 * 6 = 12`. Matches.

**Cells, by listing.** The row sequence over `t = 0..11` has period `4`: `1 2 3 2`. The column sequence has period `6`: `1 2 3 4 3 2`. Pairing them at each step:

| t | row | col | cell |
|---|---|---|---|
| 0 | 1 | 1 | (1,1) |
| 1 | 2 | 2 | (2,2) |
| 2 | 3 | 3 | (3,3) |
| 3 | 2 | 4 | (2,4) |
| 4 | 1 | 3 | (1,3) |
| 5 | 2 | 2 | (2,2) |
| 6 | 3 | 1 | (3,1) |
| 7 | 2 | 2 | (2,2) |
| 8 | 1 | 3 | (1,3) |
| 9 | 2 | 4 | (2,4) |
| 10 | 3 | 3 | (3,3) |
| 11 | 2 | 2 | (2,2) |

Distinct cells: `(1,1), (2,2), (3,3), (2,4), (1,3), (3,1)`. That is `6`. Matches.

**Cells, by formula.** With `M = 2`:
- `qa = 1`, `ra = 0`, `qb = 1`, `rb = 1`.
- `P1 = 6`, `P2 = 6`, `P12 = 6` (here `g = 1`, so every `u` is a multiple of `g`).
- Answer `6 + 6 - 6 = 6`. Matches.

Notice that `P12 = P1` here. The cells `(2,2)`, `(1,3)` and the others are all visited, and the formula counts the same thing the table shows.

## Pitfalls

- Using `lcm(n, m)` instead of `lcm(n-1, m-1)` for the steps. The period is set by the bounces, not the grid size.
- Forgetting that the ball visits a cell with two possible phases (going down or going up). Counting only the "down" phase undercounts.
- Forgetting `P12`. The pairs with `u ≡ v` and `u ≡ -v` are counted twice without it.
- Overflow in the `lcm` and in the residue products. Use `__int128` for the intermediate products.
- Checking only the sample `3 4`. The sample `19 18` checks the closed form for the cell count with `g = 1`, which is the common case.

## Complexity

- Time: `O(log)` per test (gcd and a constant number of divisions).
- Space: `O(1)`.

## Key takeaway

A bouncing ball is two clocks with different periods. The steps are the lcm of the periods. The distinct cells are the pairs of clock readings that can line up, counted by residues modulo the shared tick.
