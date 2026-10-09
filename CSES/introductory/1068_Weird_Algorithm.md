# [1068 — Weird Algorithm](https://cses.fi/problemset/task/1068)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** simulation, Collatz  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider an algorithm that takes as input a positive integer `n`. If `n` is even, the algorithm divides it by two, and if `n` is odd, the algorithm multiplies it by three and adds one. The algorithm repeats this, until `n` is one. For example, the sequence for `n=3` is as follows:
`3 -> 10 -> 5 -> 16 -> 8 -> 4 -> 2 -> 1`
Your task is to simulate the execution of the algorithm for a given value of `n`.

### Input

The only input line contains an integer `n`.

### Output

Print a line that contains all values of `n` during the algorithm.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
3
```

**Output**
```
3 10 5 16 8 4 2 1
```

---

## What this problem really is

Simulate the Collatz / “3n+1” process from `n` down to `1`, printing every value including the start and the final `1`.

## What to use

| Tool | Why |
|------|-----|
| Simple loop | Follow the rule until `1` |
| `long long` | Intermediate values can exceed `n` (and `int`) even if start `n <= 1e6` |
| Parity check | Even → `/2`, odd → `3*n+1` |

## Approach

**Root idea:** there is no shortcut required — apply the rule and print. Use a wide integer type because `3*n+1` can grow before falling.

**Mental picture:** a hailstone path — bounce up on odds, halve on evens, until you hit 1.

**Steps**

1. Read `n` as `long long`.
2. Print `n`. While `n != 1`: if `n` even `n /= 2`, else `n = 3*n+1`; print each new `n`.

## Walkthrough (on a sample)

`3` (odd) → `10` → `5` → `16` → `8` → `4` → `2` → `1`.

## Pitfalls

- Using `int` — overflow on the way up.
- Stopping before printing `1`, or skipping the initial `n`.
- Integer division truncating incorrectly (use even check carefully).

## Complexity

- Time: proportional to Collatz steps (tiny for these constraints)  
- Space: `O(1)`

## Key takeaway

**Weird Algorithm = Collatz simulation with `long long`** — print every value until `1`.
