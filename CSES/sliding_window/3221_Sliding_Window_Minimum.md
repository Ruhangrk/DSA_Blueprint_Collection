# [3221 — Sliding Window Minimum](https://cses.fi/problemset/task/3221)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, deque, monotonic queue  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the minimum of each window of `k` elements, from left to right.

In this problem the input data is large and it is created using a generator.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

The next line contains four integers `x`, `a`, `b` and `c`: the input generator parameters. The input is generated as follows:

- `x1 = x`
- `xi = (a * x_{i-1} + b) mod c` for `i = 2,3,...,n`

### Output

Print the xor of all window minimums.

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
3
```

Explanation: The input array is `[3,0,1,8,2,4,7,6]`. The windows are `[3,0,1,8,2]`, `[0,1,8,2,4]`, `[1,8,2,4,7]` and `[8,2,4,7,6]`, and their minimums are `0`, `0`, `1` and `2`. Thus, the answer is `0 xor 0 xor 1 xor 2 = 3`.

---

## What this problem really is

Stream length `n<=1e7`, report min of every length-`k` window, XOR those mins. Need `O(1)` amortized per step.

## What to use

| Tool | Why |
|------|-----|
| Monotonic increasing deque of indices | Front = current window minimum |
| LCG generator | Values on the fly |
| Expire by index | Pop front when `idx <= i-k` |

## Approach

**Root idea:** keep candidates for future minima in increasing order of value. A new value pops every worse (larger-or-equal) candidate from the back — they can never be min while this value is still in the window.

**Mental picture:** a queue of “hopeful minima.” Bigger latecomers kick out larger hopefuls behind them; anyone who slid out of the window leaves the front.

**How that becomes the method**

1. For `i = 0..n-1`, generate `v`.
2. While deque back has `value >= v`, pop back.
3. Push `(i, v)`.
4. While deque front index `<= i-k`, pop front.
5. If `i >= k-1`, `ans ^= deque.front().value`.

**Data structures:** deque of `(index, value)` pairs (or store values in a ring and only indices in the deque).

## Walkthrough (on a sample)

Mins `0,0,1,2` → XOR `3`.

## Pitfalls

- Storing only values without indices (cannot expire correctly when duplicates exist).  
- `O(nk)` sparse table rebuild per window.  
- Using `multiset` (`O(n log k)`) — may TLE at `1e7`.

## Complexity

- Time: `O(n)`  
- Space: `O(k)`

## Key takeaway

**Sliding window minimum = monotonic deque** — front is the min; XOR all of them.
