# [3220 — Sliding Window Sum](https://cses.fi/problemset/task/3220)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, streaming, LCG  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the sum of each window of `k` elements, from left to right.

In this problem the input data is large and it is created using a generator.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

The next line contains four integers `x`, `a`, `b` and `c`: the input generator parameters. The input is generated as follows:

- `x1 = x`
- `xi = (a * x_{i-1} + b) mod c` for `i = 2,3,...,n`

### Output

Print the xor of all window sums.

### Constraints

- `1 <= k <= n <= 1e7`
- `0 <= x, a, b <= 1e9`
- `1 <= c <= 1e9`

### Examples

**Input**
```
8 5
3 7 1 11
```

**Output**
```
12
```

Explanation: The input array is `[3,0,1,8,2,4,7,6]`. The windows are `[3,0,1,8,2]`, `[0,1,8,2,4]`, `[1,8,2,4,7]` and `[8,2,4,7,6]`, and their sums are `14`, `15`, `22` and `27`. Thus, the answer is `14 xor 15 xor 22 xor 27 = 12`.

---

## What this problem really is

Stream an LCG of length `n<=1e7`, maintain every length-`k` sum, XOR those sums together. Cannot store a full `O(n)` approach that is too slow/heavy without care — need `O(n)` time and ideally `O(k)` or `O(1)` extra memory tricks.

## What to use

| Tool | Why |
|------|-----|
| Running sum | `sum += enter; sum -= leave` |
| Ring buffer of size `k` | Remember the value that falls out |
| 64-bit sum / xor | Window sums can be large |

## Approach

**Root idea:** consecutive windows differ by one exit and one entry. Update the sum in `O(1)` per step; fold into a running XOR of answers.

**Mental picture:** a fixed-width box sliding along a conveyor. Add the new item’s weight, subtract the item that just fell off the back.

**How that becomes the method**

1. Generate values on the fly: `cur = x`, then repeatedly `cur = (a*cur + b) % c`.
2. Keep a circular buffer `buf[k]` of the last `k` values (or two pointers on the LCG if you regenerate the leaving value — buffer is simplest).
3. Build first window sum; `ans ^= sum`.
4. For each new value: `sum += new - buf[i%k]`; store `new` in buffer; `ans ^= sum`.
5. Print `ans`.

**Data structures:** `vector`/`array` of size `k`; `long long sum`, `ans`.

## Walkthrough (on a sample)

Windows sums `14,15,22,27` → XOR `12`.

## Pitfalls

- Allocating `vector` of size `n=1e7` unnecessarily (memory OK at 512MB but wasteful).  
- 32-bit sum overflow.  
- Off-by-one on number of windows `n-k+1`.

## Complexity

- Time: `O(n)`  
- Space: `O(k)`

## Key takeaway

**Sliding window sum = maintain running sum** with enter/leave updates; XOR aggregates.
