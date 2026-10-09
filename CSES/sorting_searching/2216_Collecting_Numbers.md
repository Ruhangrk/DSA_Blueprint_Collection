# [2216 — Collecting Numbers](https://cses.fi/problemset/task/2216)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** permutation, positions, counting  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array that contains each number between `1 ... n` exactly once. Your task is to collect the numbers from `1` to `n` in increasing order.

On each round, you go through the array from left to right and collect as many numbers as possible. What will be the total number of rounds?

### Input

The first line has an integer `n`: the array size.

The next line has `n` integers `x1, x2, ..., xn`: the numbers in the array.

### Output

Print one integer: the number of rounds.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
5
4 2 1 5 3
```

**Output**
```
3
```

---

## What this problem really is

Permutation of `1..n`. Each round, walking left→right, you pick the next needed integer whenever you see it. Count how many passes until `n` is collected.

## What to use

| Tool | Why |
|------|-----|
| `pos[v]` = index of value `v` | Compare where consecutive values sit |
| One scan over values `1..n-1` | A new round starts whenever `k+1` appears **left** of `k` |
| No simulation of rounds | Simulating each pass would be too slow in the worst case |

## Approach

**Root idea:** in one left-to-right pass you can collect a consecutive chain `k, k+1, k+2, ...` as long as each next value sits **to the right** of the previous one. Whenever `pos[k+1] < pos[k]`, you must start a new round to pick `k+1`.

**Mental picture:** the numbers `1..n` are checkpoints on the line. You jog left→right grabbing the next checkpoint only if it’s ahead; if the next one is behind you, you go home and start another jog.

**Data structures**

- Array `pos[1..n]`: `pos[x[i]] = i` (1-based index).

**Steps**

1. Build `pos` from the array.
2. `rounds = 1`.
3. For `k = 1..n-1`: if `pos[k+1] < pos[k]`, `rounds++`.
4. Print `rounds`.

## Walkthrough (on a sample)

Array `4 2 1 5 3` → positions: `1@3, 2@2, 3@5, 4@1, 5@4`.

- `2` left of `1` → new round  
- `3` right of `2` → same round  
- `4` left of `3` → new round  
- `5` right of `4` → same round  

Start with round 1 for collecting `1`, plus two breaks → **3** rounds.

Order collected: round1: `1` then (next pass) wait — actually: round1 can get `1` then nothing else needed that’s right of it in order… Using the formula: breaks at `1→2` and `3→4` → `1+2=3`. ✓

## Pitfalls

- Counting breaks wrong: rounds = `1 +` (number of descents in position along `1..n`).
- Simulating rounds by rescanning the array each time — `O(n^2)` worst case.
- 0-based vs 1-based `pos` confusion.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Collecting `1..n` in passes = `1 +` count of times `pos[k+1] < pos[k]`** — each descent forces a new round.
