# [1742 — Robot Path](https://cses.fi/problemset/task/1742)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** sweep, segments, first revisit  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a description of a robot's path. The robot begins at point `(0,0)` and performs `n` commands. Each command moves the robot some distance up, down, left or right.

The robot will stop when it has performed all commands, or immediately when it returns to a point that it has already visited. Your task is to calculate the total distance the robot moves.

### Input

The first line has an integer `n`: the number of commands.

After that, there are `n` lines describing the commands. Each line has a character `d` and an integer `x`: the robot moves the distance `x` to the direction `d`. Each direction is `U` (up), `D` (down), `L` (left), or `R` (right).

### Output

Print the total distance the robot moves.

### Constraints

- `1 <= n <= 1e5`
- `1 <= x <= 1e6`

### Examples

**Input**
```
5
U 2
R 3
D 1
L 5
U 2
```

**Output**
```
9
```

---

## What this problem really is

Axis-aligned polyline from the origin; walk along it and stop at the **first lattice point that already appeared earlier** on the path (including the start). Report arc length until that stop (or full length if never revisited).

## What to use

| Tool | Why |
|------|-----|
| Expand commands to segments | Each move is a horizontal or vertical segment |
| “First hit” against past trail | For the current segment, find nearest previously drawn point on it |
| Sweep / set of active segments | `n` large and lengths large — cannot stamp every cell |

## Approach

**Root idea:** the path is a sequence of H/V segments. Before drawing segment `i`, the forbidden set is the union of points on segments `0..i-1` (and the start). The answer truncates segment `i` at the closest previously visited lattice point along the travel direction (if any).

**Mental picture:** draw the route with a pen that cannot cross its own ink. As soon as the tip touches old ink, it freezes; total ink length drawn is the answer.

**How that becomes the method**

1. Simulate position `(cx,cy)` starting at `(0,0)`. Build the list of full intended segments, but you will clip them on the fly.
2. Maintain data structures of **already finalized** horizontal and vertical unit-covered ranges (as segments on lines `y = const` or `x = const`):
   - map `y ->` set of disjoint x-intervals already visited on that row
   - map `x ->` set of disjoint y-intervals already visited on that column
3. For a new move in direction `d` of length `L`, binary-search / scan the relevant map to find the nearest previously covered point strictly ahead of the start (the start of this move was just visited by the previous move — revisiting means a point that appeared **before** the current open move; careful: the endpoint you leave from is already visited, so “return to a point already visited” means hitting some point again after leaving — typically the first **new** cell that collides with the past, not the cell you are standing on).
4. Practically: the robot continuously occupies lattice points. After finishing a positive step onto a new point, if that point was seen before, stop. So for a move of length `L`, check the `L` successive points; the first already-in-set stops. With large `L`, query the segment structures for the earliest intersection in the travel direction, at distance `d_hit` in `1..L`. Travel `d_hit`, add `d_hit` to answer, stop. If none, travel `L`, insert the new segment into the maps, continue.
5. Include `(0,0)` in the visited set from the beginning.

Efficient implementation details vary (set of intervals per row/col, or sweep for self-intersection of orthogonal segments). The invariant is: answer = distance until first repeated lattice point.

**Data structures:** `map<int, set of intervals>` for horizontal and vertical coverage; current pose; 64-bit distance (up to `1e5 * 1e6`).

## Walkthrough (on a sample)

Path goes up 2, right 3, down 1, then left. While going left it crosses earlier ink and stops so that total length is `9` (not the full command sum).

## Pitfalls

- Stopping at the cell you already stand on at the start of a move.  
- Missing that only **lattice points** matter (integer coordinates).  
- `O(total length)` simulation — TLE.  
- 32-bit distance overflow.  
- Not inserting the partial last segment when you stop mid-move (you still count that distance).

## Complexity

- Time: `O(n log n)` with interval sets / careful merges  
- Space: `O(n)`

## Key takeaway

**Self-hitting orthogonal path = walk segments against stored H/V coverage**; stop at first repeated lattice point and report distance.
