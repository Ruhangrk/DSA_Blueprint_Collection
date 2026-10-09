# [2164 — Josephus Queries](https://cses.fi/problemset/task/2164)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Josephus, recursion, math  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a game where there are `n` children (numbered `1, 2, ..., n`) in a circle. During the game, every second child is removed from the circle, until there are no children left.

Your task is to process `q` queries of the form: “when there are `n` children, who is the `k` th child that will be removed?”

### Input

The first input line has an integer `q`: the number of queries.

After this, there are `q` lines that describe the queries. Each line has two integers `n` and `k`: the number of children and the position of the child.

### Output

Print `q` integers: the answer for each query.

### Constraints

- `1 <= q <= 1e5`
- `1 <= k <= n <= 1e9`

### Examples

**Input**
```
4
7 1
7 3
2 2
1337 1313
```

**Output**
```
2
6
1
1107
```

---

## What this problem really is

Josephus process with every 2nd child removed (same rule as Josephus Problem I), but `n` up to `1e9` and many queries ask for the `k`-th removed child — cannot simulate with a list.

## What to use

| Tool | Why |
|------|-----|
| Recursive / closed structure | Even and odd rounds have regular patterns |
| `O(log n)` per query | `n` halves conceptually each phase |
| Careful indexing | 1-based children; `k`-th removal |

## Approach

**Root idea:** the first batch of removals hits every second child (mostly the even labels). Those answers are explicit. Later removals happen among the survivors, which form a Josephus instance of size about `n/2` — solve recursively, then map the survivor-index back to the original labels.

**Mental picture:** wipe out every other seat in one sweep; the remaining seats form a smaller circle whose removal order you already know how to query; renumber back.

**Recurrence** `f(n,k)` = child removed at step `k` (1-based):

```
if (n == 1) return 1;

if (k <= (n+1)/2) {
  // first wave: mostly 2,4,6,...
  if (2*k > n) return (2*k) % n;   // wrap when n is odd
  else return 2*k;
}

// later waves: recurse on floor(n/2) survivors
x = f(n/2, k - (n+1)/2);
if (n % 2 == 1) return 2*x + 1;
else            return 2*x - 1;
```

Each call roughly halves `n` → `O(log n)` per query.

**Steps**

1. For each query, print `f(n,k)`.

## Walkthrough (on a sample)

`n=7`: removal order `2,4,6,1,5,3,7` (same as Josephus I). So `k=1` → **2**, `k=3` → **6**.

`n=2,k=2` → second removed is **1**.

## Pitfalls

- Simulating with queues for `n=1e9` — impossible.
- Off-by-one when mapping survivors’ indices back.
- Assuming first `n/2` removals are always `2,4,...,` even when `n` is odd (the wrap-around after the last even affects order of later odds).

## Complexity

- Time: `O(q log n)`  
- Space: `O(log n)` recursion

## Key takeaway

**`k`-th Josephus removal for huge `n` = recursive halving** — answer even-wave removals directly, then remap a smaller query onto the survivor circle.
