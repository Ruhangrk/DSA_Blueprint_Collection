# [1757 — Course Schedule II](https://cses.fi/problemset/task/1757)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** topological sort, priority queue, DAG  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You want to complete `n` courses that have requirements of the form "course `a` has to be completed before course `b`".

You want to complete course `1` as soon as possible. If there are several ways to do this, you want then to complete course `2` as soon as possible, and so on.

Your task is to determine the order in which you complete the courses.

### Input

The first input line has two integers `n` and `m`: the number of courses and requirements. The courses are numbered `1,2,...,n`.

Then, there are `m` lines describing the requirements. Each line has two integers `a` and `b`: course `a` has to be completed before course `b`.

You can assume that there is at least one valid schedule.

### Output

Print one line having `n` integers: the order in which you complete the courses.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a,b <= n`

### Examples

**Input**
```
4 2
2 1
2 3
```

**Output**
```
2 1 3 4
```

---

## What this problem really is

Find a topological order of a DAG that lexicographically minimizes `(pos[1], pos[2], ..., pos[n])` — finish smaller course IDs as early as possible, in ID order as tie-break priority.

## What to use

| Tool | Why |
|------|-----|
| Reverse edges | Scheduling from the end makes “leave small IDs free early” natural |
| Max-heap Kahn | Among courses that can be last, finish the largest ID last |
| Reverse the built list | Convert “last-to-first” construction into the real order |

## Approach

**Root idea:** minimizing positions of `1`, then `2`, … is *not* the same as “always take the smallest currently available course” in the forward graph. Build the order from the **end**: among nodes with no remaining successors, place the largest label as late as possible.

**Mental picture:** you fill the schedule calendar from Friday backward. Whenever several courses could be the last remaining one, dump the biggest course number onto Friday so smaller numbers stay available for earlier slots.

**How that becomes the method**

1. Build the **reversed** graph: for requirement `a` before `b` (edge `a -> b` in the real graph), store reverse edge `b -> a`. Track indegrees in the reversed graph.
2. Push all nodes with reversed-indegree `0` into a **max**-heap.
3. Repeatedly pop the largest node `u`, append `u` to a list `tail` (these are courses in reverse completion order), and relax reverse-neighbors.
4. Reverse `tail` to get the completion order.
5. (Guaranteed valid by statement.)

**Why this matches the goal:** delaying large IDs pushes small IDs leftward whenever prerequisites allow.

**Data structures:** `adj_rev`, `indeg[n+1]`, `priority_queue` max-heap, answer vector.

## Walkthrough (on a sample)

Requirements: `2` before `1`, `2` before `3`. Real edges `2->1`, `2->3`. Reverse: `1->2`, `3->2`. Indegrees (rev): `1:0, 2:2, 3:0, 4:0`.

- Heap `{4,3,1}` → pop `4`, then `{3,1}` → pop `3`, then `{1}` → pop `1`, unlocks `2` → pop `2`.
- `tail = [4,3,1,2]` → reverse → `2 1 3 4`.

Course `1` is as early as prerequisites allow (right after `2`); then `2` is already first; then `3` before `4`.

## Pitfalls

- Using forward min-heap Kahn (lex-smallest sequence of IDs) — different objective; fails some tests even if it passes the sample.
- Forgetting to reverse the edges *and* the output.
- `n=1e5` needs heap Kahn, not `O(n^2)`.

## Complexity

- Time: `O((n+m) log n)`
- Space: `O(n+m)`

## Key takeaway

“Finish course `1`, then `2`, … ASAP” → reverse the DAG and always schedule the largest remaining sink last.
