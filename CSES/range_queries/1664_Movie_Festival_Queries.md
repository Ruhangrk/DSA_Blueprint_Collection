# [1664 — Movie Festival Queries](https://cses.fi/problemset/task/1664)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** greedy, binary lifting, sparse table on time  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

In a movie festival, `n` movies will be shown. You know the starting and ending time of each movie.

Your task is to process `q` queries of the form: if you arrive and leave the festival at specific times, what is the maximum number of movies you can watch?

You can watch two movies if the first movie ends before or exactly when the second movie starts. You can start the first movie exactly when you arrive and leave exactly when the last movie ends.

### Input

The first input line has two integers `n` and `q`: the number of movies and queries.

After this, there are `n` lines describing the movies. Each line has two integers `a` and `b`: the starting and ending time of a movie.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`: your arrival and leaving time.

### Output

Print the maximum number of movies for each query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= a < b <= 1e6`

### Examples

**Input**
```
4 3
2 5
6 10
4 7
9 10
5 9
2 10
7 10
```

**Output**
```
0
2
1
```

---

## What this problem really is

Many queries of classic **interval scheduling**: max non-overlapping movies inside a time window `[arrive, leave]`. Movies may touch at endpoints.

## What to use

| Tool | Why |
|------|-----|
| Greedy “earliest ending” | Optimal next movie from a given free time |
| `nextTime[t]` | After becoming free at `t`, when do you finish the best next movie? |
| Binary lifting on time | Jump `2^k` movies in `O(1)` / `O(log)` per query |
| Times `<= 1e6` | Arrays indexed by time are fine |

## Approach

**Root idea:** from any free time `t`, the optimal move is to watch the movie that **starts at or after `t` and ends earliest**. Precompute that finishing time for every `t`, then chain jumps with binary lifting. A query is “how many jumps from `a` until you would finish after `b`?”

**Mental picture:** a teleport table on the timeline — standing at time `t`, one button sends you to the end of the smartest next film; `log` buttons skip 2, 4, 8, … films.

**Data structures**

1. For each start time, track the best (minimum) end among movies with that start — or scan movies and, for each movie `[s,e]`, improve an array `bestEnd[s] = min(bestEnd[s], e)`.
2. Build `finish[t]` = minimum end of a movie with start `>= t` (suffix-min from the right on `bestEnd`, then `finish[t] = that minimum end`, or `INF` if none).
   - Equivalently: after filling `bestEnd[s]`, for `t = MAX..1`: propagate `bestEnd[t] = min(bestEnd[t], bestEnd[t+1])`, then `finish[t] = bestEnd[t]`.
3. Binary lifting: `up[0][t] = finish[t]` (time after 1 movie), `up[j][t] = up[j-1][up[j-1][t]]` (clamp `INF`).

**Answer query `(a,b)`**

- Start `t = a`, `ans = 0`.
- From high bit to low: if `up[j][t] != INF` and `up[j][t] <= b`, take the jump: `t = up[j][t]`, `ans += 1<<j`.
- Print `ans`.

(Only movies with end `<= b` are allowed; start must be `>= a`, which is built into beginning at `t = a`.)

## Walkthrough (on a sample)

Movies: `[2,5], [6,10], [4,7], [9,10]`.

From time `2`, earliest-ending feasible movie is `[2,5]` (ends `5`); then from `5`, best is `[6,10]` or `[9,10]` — earliest end among starts `>= 5` is `10`. Window `[2,10]` fits both → **2**.

`[5,9]`: from `5`, next finish is `10 > 9` → **0**.

`[7,10]`: movie `[9,10]` → **1**.

## Pitfalls

- Requiring strict `<` between end and next start — problem allows equal times.
- Binary lifting past `b` — must check `up[j][t] <= b` before jumping.
- Times up to `1e6`: allocate `MAX+2`, careful with `INF` sentinel.
- Building `finish[t]` wrong (only movies starting exactly at `t`, not `>= t`).

## Complexity

- Time: `O(T log n + (n + q) log T)` with `T = 1e6` (or `O((n+q) log)` with careful build)  
- Space: `O(T log T)` for lifting (or compress if desired)

## Key takeaway

**Max movies in a time window = greedy next film + binary lifting on the timeline** — from arrival, jump while finish `<=` leave.
