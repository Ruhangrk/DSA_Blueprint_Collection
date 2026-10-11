# [2402 — Two Stacks Sorting](https://cses.fi/problemset/task/2402)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Greedy, stacks, simulation, best fit  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have an input list of `n` numbers (each from `1` to `n` exactly once). Using two stacks, produce a sorted output list. Each move is either:

- move the first number of the input list onto a stack, or
- move the top of a stack to the end of the output list.

For each number, print the stack it was placed on (1 or 2). Any valid answer is accepted. If there is none, print `IMPOSSIBLE`.

### Input

- The first line has `n`.
- The second line has the input list.

### Output

Print `n` integers, each `1` or `2`, or `IMPOSSIBLE`.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
5
2 3 1 5 4
```

**Output**
```
1 2 1 1 2
```

Any valid answer is accepted. The walkthrough below gives a different valid answer, `1 2 1 1 1`.

---

## What this problem really is

The output must be `1, 2, 3, ..., n` in order. So the next number to output is always the smallest one not yet output. A stack only exposes its top, so a stack can only release numbers in order if its contents are arranged so that the smallest one is always on top. So each stack must hold numbers that decrease from bottom to top.

The question is only: can we split the input into two stacks that each stay in that order?

## What to use

| Tool | Why |
|------|-----|
| Two stacks with their tops | The only information needed to decide where the next number goes |
| `next` = smallest number not yet output | Pop a stack whenever its top equals `next` |
| Best fit rule | When a number fits on both stacks, pick the one with the smaller top |

## Approach

**Root idea:** a stack is valid if every number on it is smaller than the number below it, since the smaller number has to leave first. So when we push `x`, the top of that stack must be greater than `x`. Whenever a stack top equals `next`, it can be output immediately, which is always safe.

**Greedy:**

1. Set `next = 1`. Both stacks start empty; an empty stack has top `infinity`.
2. For each input number `x` in order:
   - Among stacks whose top is greater than `x`, pick the one with the **smallest top**. This is the best fit.
   - If no stack qualifies, print `IMPOSSIBLE`.
   - Push `x` onto the chosen stack and record its number.
   - While either stack's top equals `next`, pop it and increase `next`.

**Why best fit works:** putting `x` on the stack with the smallest top that is still larger than `x` keeps the other stack's top as large as possible. A large top is the most flexible for future numbers, because more numbers fit under it.

**Mental picture:** two stacks of plates on a counter. Each plate must be smaller than the plate below it. Plates are given to you in a fixed order. Put each plate on the stack where it sits closest to the plate beneath it. Whenever the smallest unused plate is at the top of a stack, remove it and send it to the output pile.

## Walkthrough (on the sample)

Input: `2 3 1 5 4`, `next = 1`.

| x | Stack 1 (bottom to top) | Stack 2 (bottom to top) | Choice | Pops after | next |
|---|-------------------------|-------------------------|--------|-----------|------|
| 2 | [] | [] | both empty: take stack 1 | none, top is 2 | 1 |
| 3 | [2] | [] | stack 1 top 2 < 3, cannot fit; stack 2 empty, use it | none, tops are 2 and 3 | 1 |
| 1 | [2] | [3] | both tops > 1; smaller top is stack 1 (2) | pop 1, pop 2 (stack 1), pop 3 (stack 2) | 4 |
| 5 | [] | [] | stack 1 (empty) | none | 4 |
| 4 | [5] | [] | stack 1 top 5 > 4; stack 2 empty treated as infinity, so stack 1 | pop 4, pop 5 | 6 |

Assignments in input order: `2 -> 1`, `3 -> 2`, `1 -> 1`, `5 -> 1`, `4 -> 1`.

Output: `1 2 1 1 1`. This differs from the sample output `1 2 1 1 2`, but it is valid: the output order is `1, 2, 3, 4, 5`, which is sorted. The problem accepts any valid answer.

## Pitfalls

- Not popping immediately when the top equals `next`. Delaying the pop can block later numbers unnecessarily.
- Choosing the stack with the larger top (worst fit). That wastes the flexibility of the other stack.
- Forgetting that an empty stack accepts anything. Treat its top as infinity.
- Printing `IMPOSSIBLE` too early. Only fail when no stack has a top greater than `x`.
- Forgetting to check the final state. If every number was placed, the pops clear everything, so the answer is valid.

## Complexity

- Time: `O(n)` for the simulation, since each number is pushed and popped once.
- Space: `O(n)`.

## Key takeaway

Two stacks produce sorted output exactly when every stack keeps its contents decreasing from bottom to top. Simulate it greedily: pop whenever the next needed number is on top, and push each number onto the stack whose top is the smallest one still larger than it.
