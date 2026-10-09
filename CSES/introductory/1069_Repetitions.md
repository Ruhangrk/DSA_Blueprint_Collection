# [1069 — Repetitions](https://cses.fi/problemset/task/1069)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** strings, scanning  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a DNA sequence: a string consisting of characters `A`, `C`, `G`, and `T`. Your task is to find the longest repetition in the sequence. This is a maximum-length substring containing only one type of character.

### Input

The only input line contains a string of `n` characters.

### Output

Print one integer: the length of the longest repetition.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
ATTCGGGA
```

**Output**
```
3
```

---

## What this problem really is

Longest run of identical characters in a string (max consecutive streak).

## What to use

| Tool | Why |
|------|-----|
| One linear scan | Compare each char to the previous |
| Counters `cur`, `best` | Track current run and global max |

## Approach

**Root idea:** walk left to right; extend the current run when the character matches the previous one, otherwise reset the run to 1. Keep the maximum run length.

**Mental picture:** counting how long each “block” of the same letter lasts; report the longest block.

**Steps**

1. `cur = best = 1`.
2. For each index `i = 1..n-1`: if `s[i]==s[i-1]` then `cur++`, else `cur=1`; `best = max(best, cur)`.
3. Print `best` (for `n=1`, answer is `1`).

## Walkthrough (on a sample)

`ATTCGGGA`: runs of lengths `1,2,1,3,1` → longest **3** (`GGG`).

## Pitfalls

- Forgetting `n=1`.
- Counting total frequency of a letter instead of a contiguous run.

## Complexity

- Time: `O(n)`  
- Space: `O(1)` extra

## Key takeaway

**Longest same-char streak = one scan with a current-run counter.**
