# [1632 — Movie Festival II](https://cses.fi/problemset/task/1632)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** greedy, multiset, interval scheduling  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

In a movie festival, `n` movies will be shown. Syrjälä's movie club consists of `k` members, who will be all attending the festival.

You know the starting and ending time of each movie. What is the maximum total number of movies the club members can watch entirely if they act optimally?

### Input

The first input line has two integers `n` and `k`: the number of movies and club members.

After this, there are `n` lines that describe the movies. Each line has two integers `a` and `b`: the starting and ending time of a movie.

### Output

Print one integer: the maximum total number of movies.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= a < b <= 1e9`

### Examples

**Input**
```
5 2
1 5
8 10
3 6
2 5
6 9
```

**Output**
```
4
```

---

## What this problem really is

Assign movies to up to `k` people (each person watches a non-overlapping sequence). Maximize the **total** number of (person, movie) assignments. Same movie can’t be watched by two people (each showing is one slot).

## What to use

| Tool | Why |
|------|-----|
| Sort movies by ending time | Classic scheduling greedy, extended to `k` workers |
| `multiset` of members’ free times | Who becomes free earliest / pick best member for a movie |
| Greedy assignment | Take earliest-ending movie; assign to a member free by its start |

## Approach

**Root idea:** sort movies by end time ascending. For each movie in that order, assign it to a member who is already free at its start — among such members, pick the one with the **latest** free time that is still `<= start` (tightest fit), so earlier-free members stay available for awkward earlier starts. If nobody is free, skip the movie unless you still have an unused member slot.

**Mental picture:** `k` club badges, each remembering “busy until”. Walk movies that finish soonest; hand each to the badge that freed up most recently but still in time.

**Data structures**

- Movies `{start, end}` sorted by `end`.
- `multiset<int> free` — current free-time of each active member (initially empty, or `k` zeros).
- Often: start with empty multiset; when assigning, if you can reuse someone, erase/reinsert; else if `free.size() < k`, add a new member.

**Concrete steps**

1. Sort by increasing end.
2. `multiset<int> end_times` of assigned members’ current end (when they become free).
3. For each movie `(s,e)`:
   - Find a member with free time `<= s`: `upper_bound(s)` then step left (largest free `<= s`).
   - If found: erase that free time, insert `e` (they now free at `e`); `ans++`.
   - Else if fewer than `k` members used: insert `e`; `ans++`.
   - Else: skip.
4. Print `ans`.

(Equivalent: multiset of size exactly `k` initialized to `0`.)

## Walkthrough (on a sample)

`k=2`, movies sorted by end. Two members can cover four movies total (e.g. one watches two, the other watches two) → **4**.

## Pitfalls

- Assigning to the member free **earliest** among those with `free <= s` can be wrong — prefer the **latest** free `<= s` (upper_bound then `--`).
- Allowing two members to watch the same movie.
- Sorting by start instead of end.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**`k`-person interval scheduling = sort by end time + multiset of free times**, assigning each film to the tightest feasible member.
