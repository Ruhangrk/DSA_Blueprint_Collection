# [1084 — Apartments](https://cses.fi/problemset/task/1084)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** sorting, two pointers, greedy matching  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` applicants and `m` free apartments. Your task is to distribute the apartments so that as many applicants as possible will get an apartment.

Each applicant has a desired apartment size, and they will accept any apartment whose size is close enough to the desired size.

### Input

The first input line has three integers `n`, `m`, and `k`: the number of applicants, the number of apartments, and the maximum allowed difference.

The next line contains `n` integers `a1, a2, ..., an`: the desired apartment size of each applicant. If the desired size of an applicant is `x`, they will accept any apartment whose size is between `x-k` and `x+k`.

The last line contains `m` integers `b1, b2, ..., bm`: the size of each apartment.

### Output

Print one integer: the number of applicants who will get an apartment.

### Constraints

- `1 <= n, m <= 2e5`
- `0 <= k <= 1e9`
- `1 <= ai, bi <= 1e9`

### Examples

**Input**
```
4 3 5
60 45 80 60
30 60 75
```

**Output**
```
2
```

---

## What this problem really is

Maximum matching between applicants and apartments where applicant `a` accepts apartment `b` iff `|a - b| <= k`. Each person/apartment used at most once.

## What to use

| Tool | Why |
|------|-----|
| Sort both arrays | Enables a single left-to-right greedy scan |
| Two pointers | Assign the smallest feasible apartment to the current applicant |
| Greedy | Never hurts to give the “earliest” apartment that fits |

## Approach

**Root idea:** sort desires and sizes. Walk both lists: if the current apartment is too small for the applicant (`b < a-k`), discard the apartment; if too big (`b > a+k`), skip the applicant; otherwise match them and advance both.

**Mental picture:** two sorted queues at a housing office — try the cheapest leftover flat on the next person in line; throw away flats nobody left will want, or people who can’t take what’s left.

**Data structures**

- Arrays `a[0..n)`, `b[0..m)` sorted ascending.
- Indices `i` (applicant), `j` (apartment), counter `ans`.

**Steps**

1. Sort `a` and `b`.
2. While `i < n` and `j < m`:
   - if `b[j] < a[i] - k`: `j++`
   - else if `b[j] > a[i] + k`: `i++`
   - else: match → `ans++`, `i++`, `j++`
3. Print `ans`.

## Walkthrough (on a sample)

`k=5`. Sorted desires `45,60,60,80`. Apartments `30,60,75`.

- `45` vs `30`: `30 < 40` → drop `30`.
- `45` vs `60`: `60 > 50` → skip applicant `45`.
- `60` vs `60`: fit → match (**1**).
- `60` vs `75`: `|60-75|=15 > 5` → `75 > 65` → skip this `60`.
- `80` vs `75`: `|80-75|=5` → match (**2**).

## Pitfalls

- Forgetting to sort both arrays.
- Using `abs` without sorting — still correct with other methods, but two pointers need order.
- Overflow on `a[i]-k` if using unsigned / if `k` large — use signed `long long` or careful compares (`b[j] + k < a[i]`, etc.).

## Complexity

- Time: `O(n log n + m log m)`  
- Space: `O(n + m)`

## Key takeaway

**Tolerance matching on sizes = sort both sides + two pointers** — discard too-small apartments and too-picky applicants until a pair fits.
