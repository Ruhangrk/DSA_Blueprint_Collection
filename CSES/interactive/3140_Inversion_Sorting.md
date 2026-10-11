# [3140 — Inversion Sorting](https://cses.fi/problemset/task/3140)

**Platform:** CSES  
**Source:** CSES Problem Set — Interactive Problems  
**Tags:** interactive, inversions, reverse sorting  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a hidden permutation `a1, a2, ..., an` of integers `1, 2, ..., n`. Your task is to sort the permutation by reversing subarrays.

On each turn, you can reverse a subarray of the permutation. After that, you will be reported the number of inversions in the permutation. If the number of inversions is `0` (i.e., the permutation is sorted), you win.

### Interaction

Read `n` first.

Each turn: print `i j` to reverse the inclusive subarray from `i` to `j`.

Then read one integer: the inversion count after that reverse. If it is `0`, you have won — terminate immediately.

### Constraints

- `1 <= n <= 1000`
- at most `4n` operations

### Examples

```
3
1 2
1
2 3
0
```

Explanation: Start `[3,1,2]` → reverse `[1..2]` → `[1,3,2]` (1 inv) → reverse `[2..3]` → sorted.

---

## What this problem really is

Sort a hidden perm using subarray reverses; the only feedback is the inversion count after each move. Budget about four moves per index.

## What to use

| Tool | Why |
|------|-----|
| Selection by reversals | Once you know where the next value sits, one reverse places it |
| Δinv as a probe | A trial reverse changes inv in a way that encodes structure of the segment |
| Cap `4n` | Roughly a small constant of probes + one placing reverse per position |

## Approach

**Root idea:** sort left to right. After values `1..p-1` sit in positions `1..p-1`, find index `j` of value `p` in the suffix and reverse `[p..j]` to place it. The inv oracle replaces “reading” the array: trial reverses (and optional undo reverses) locate `j` cheaply enough to stay in `4n`.

**Mental picture:** blind pancake skills — flip a spatula range, hear only “how unsorted” (inversion count), use that tone to home in on the next ingredient, then flip it into place.

**How that becomes the method**

1. For `p = 1..n-1` (stop early whenever the grader returns `0`):
   - Locate `j =` position of the minimum remaining value (which must be `p`) inside `[p..n]` by binary / exponential search with trial reverses on prefixes of the suffix, reading Δinv (undo with a second reverse when the probe should not stick).
   - Permanently reverse `[p..j]` to seat `p`.
2. Each placed index uses a handful of probes + one commit reverse — keep the probe policy tight so the sum ≤ `4n`.

Why inv works as a probe: reversing a segment reorders a known set of pairs; whether the current minimum of a suffix sits inside a candidate range changes the Δinv in a distinguishable way compared to it sitting outside.

**Data structures:** none beyond loop indices; optionally cache the last reported `inv`.

## Walkthrough (on a sample)

Reverse `[1..2]` improves structure (`inv=1`); reverse `[2..3]` finishes (`inv=0`).

## Pitfalls

- Bubble with adjacent reverses → `O(n^2)` ops, over budget.  
- Forgetting to stop when `inv` hits `0`.  
- Probe reverses left undone, scrambling a fixed prefix.

## Complexity

- Operations: `O(n)` with a tight constant (must be `<= 4n`)  
- Space: `O(1)`

## Key takeaway

**Inversion-feedback reverse sorting = selection-place with Δinv probes**, not blind `O(n^2)` adjacent swaps.
