# [1083 — Missing Number](https://cses.fi/problemset/task/1083)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** math, xor, summing  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given all numbers between `1, 2, ..., n` except one. Your task is to find the missing number.

### Input

The first input line contains an integer `n`.

The second line contains `n-1` numbers. Each number is distinct and between `1` and `n` (inclusive).

### Output

Print the missing number.

### Constraints

- `2 <= n <= 2e5`

### Examples

**Input**
```
5
2 3 1 5
```

**Output**
```
4
```

---

## What this problem really is

Find the one missing value from the set `{1..n}` given the other `n-1` values.

## What to use

| Tool | Why |
|------|-----|
| Sum formula | `missing = n*(n+1)/2 - sum(given)` |
| XOR trick | `1^2^...^n ^ all given` cancels pairs, leaves missing |
| `long long` for sum | `n*(n+1)/2` overflows `int` at `2e5` |

## Approach

**Root idea:** the complete sum (or XOR) of `1..n` is known; subtract / XOR-away everything you see; the remainder is the missing number.

**Mental picture:** a full checklist of `1..n`; tick off each input number; the unticked box is the answer.

**Steps (sum)**

1. `total = n * (n+1) / 2` as `long long`.
2. Subtract every input value from `total`.
3. Print what’s left.

**Steps (XOR)**

1. `x = 0`; XOR in all of `1..n` and all given numbers; print `x`.

## Walkthrough (on a sample)

`n=5`, numbers `2 3 1 5`. Sum should be `15`; seen sum `11`; missing **4**.

## Pitfalls

- Computing `n*(n+1)/2` in 32-bit `int`.
- Assuming the missing number is in a scanned “gap” without handling missing `n` or `1`.

## Complexity

- Time: `O(n)`  
- Space: `O(1)`

## Key takeaway

**One missing from `1..n` = expected sum (or XOR) minus what you see.**
