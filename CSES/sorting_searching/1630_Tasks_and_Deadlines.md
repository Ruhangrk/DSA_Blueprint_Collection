# [1630 — Tasks and Deadlines](https://cses.fi/problemset/task/1630)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** greedy, sorting, scheduling  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have to process `n` tasks. Each task has a duration and a deadline, and you will process the tasks in some order one after another. Your reward for a task is `d - f` where `d` is its deadline and `f` is your finishing time. (The starting time is `0`, and you have to process all tasks even if a task would yield negative reward.)

What is your maximum reward if you act optimally?

### Input

The first input line has an integer `n`: the number of tasks.

After this, there are `n` lines that describe the tasks. Each line has two integers `a` and `d`: the duration and deadline of the task.

### Output

Print one integer: the maximum reward.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a, d <= 1e6`

### Examples

**Input**
```
3
6 10
8 15
5 12
```

**Output**
```
2
```

---

## What this problem really is

Schedule all tasks in a single sequence from time `0`. Total reward = `sum (deadline_i - finish_i)`. Maximize by choosing the order.

## What to use

| Tool | Why |
|------|-----|
| Algebraic rewrite | Finish times depend on order of durations only |
| Sort by duration ascending | Optimal greedy order |
| `long long` | Sum of rewards / finish times can be large |

## Approach

**Root idea:** expand the total reward. If tasks run in some order with durations `a1, a2, ..., an` and finish times `f_j = a1+...+aj`, then

```
sum (d_i - f_i) = sum d_i - sum f_i
```

`sum d_i` is constant. Maximizing reward = **minimizing** `sum f_i`. For a sequence of durations, `sum f_i` is minimized by sorting durations **shortest first** (classic — each duration is added into all later finish times).

Deadlines never affect the optimal order — only the constant term.

**Mental picture:** every minute you spend on a task delays every task still waiting. Do short jobs first so fewer jobs sit behind a long one.

**Data structures**

- Array of durations (deadlines only for the final sum).
- Running time `t = 0`, `long long reward = 0`.

**Steps**

1. Read all `(a,d)`; sort tasks by `a` ascending (ties arbitrary).
2. `t = 0`; for each task in that order: `t += a`; `reward += d - t`.
3. Print `reward`.

## Walkthrough (on a sample)

Tasks `(6,10), (8,15), (5,12)`. Sort by duration: `5, 6, 8`.

- Finish `5`, reward `12-5=7`
- Finish `11`, reward `10-11=-1` (total 6)
- Finish `19`, reward `15-19=-4` (total **2**)

## Pitfalls

- Sorting by deadline (EDD) — wrong objective for this reward formula.
- Dropping negative-reward tasks — problem requires processing all.
- `int` overflow on `t` and `reward` (`n * 1e6` scale, but summed finishes are larger — use `long long`).

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Max sum `(deadline - finish)` = do tasks in increasing duration order** — deadlines only add a constant; minimize sum of finish times.
