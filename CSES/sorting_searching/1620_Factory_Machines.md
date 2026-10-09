# [1620 — Factory Machines](https://cses.fi/problemset/task/1620)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** binary search on answer  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A factory has `n` machines which can be used to make products. Your goal is to make a total of `t` products.

For each machine, you know the number of seconds it needs to make a single product. The machines can work simultaneously, and you can freely decide their schedule.

What is the shortest time needed to make `t` products?

### Input

The first input line has two integers `n` and `t`: the number of machines and products.

The next line has `n` integers `k1, k2, ..., kn`: the time needed to make a product using each machine.

### Output

Print one integer: the minimum time needed to make `t` products.

### Constraints

- `1 <= n <= 2e5`
- `1 <= t <= 1e9`
- `1 <= ki <= 1e9`

### Examples

**Input**
```
3 7
3 2 5
```

**Output**
```
8
```

Explanation: Machine 1 makes two products, machine 2 makes four products and machine 3 makes one product.

---

## What this problem really is

Minimize time `T` such that the machines, working in parallel, produce at least `t` items in `T` seconds. Machine `i` makes `floor(T / ki)` items in time `T`.

## What to use

| Tool | Why |
|------|-----|
| Binary search on `T` | Feasibility is monotonic: more time ⇒ at least as many products |
| `long long` | `T` up to about `min(k)*t` ≈ `1e18` |
| Careful product counting | Sum of `T/ki` can overflow if not capped |

## Approach

**Root idea:** check(`T`) = whether `sum over i of floor(T / ki) >= t`. Binary search the smallest `T` where check is true.

**Mental picture:** a clock dial — ask “if we stop at time mid, did we hit quota?”; if yes try earlier, if no try later.

**Data structures**

- Array `k[1..n]`.
- Search bounds: `lo = 1`, `hi = *min_element(k) * t` (or `1e18` safely).

**Steps**

1. While `lo < hi`: `mid = lo + (hi-lo)/2`; if machines can make `>= t` in `mid` seconds → `hi = mid`; else `lo = mid+1`.
2. When summing `mid/k[i]`, stop early if the running sum already `>= t` (avoids overflow).
3. Print `lo`.

## Walkthrough (on a sample)

`t=7`, times `3,2,5`.

- `T=7`: floors `2+3+1=6 < 7` — no.
- `T=8`: `2+4+1=7` — yes.

Minimum **8**.

## Pitfalls

- Computing `hi = 1e9 * 1e9` in `int` — overflow; use `long long`.
- Summing all `T/ki` without a cap — intermediate sum can exceed `2^63-1`.
- Searching on products instead of time — messier.

## Complexity

- Time: `O(n log (min_k * t))`  
- Space: `O(n)`

## Key takeaway

**Min time for `t` parallel products = binary search on time** with check `sum floor(T/ki) >= t`.
