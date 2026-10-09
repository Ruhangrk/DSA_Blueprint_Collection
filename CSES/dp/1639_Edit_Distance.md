# [1639 — Edit Distance](https://cses.fi/problemset/task/1639)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, strings, Levenshtein  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

The edit distance between two strings is the minimum number of operations required to transform one string into the other.

The allowed operations are:

- Add one character to the string.
- Remove one character from the string.
- Replace one character in the string.

For example, the edit distance between `LOVE` and `MOVIE` is `2`, because you can first replace `L` with `M`, and then add `I`.

Your task is to calculate the edit distance between two strings.

### Input

The first input line has a string that contains `n` characters between `A–Z`.

The second input line has a string that contains `m` characters between `A–Z`.

### Output

Print one integer: the edit distance between the strings.

### Constraints

- `1 <= n, m <= 5000`

### Examples

**Input**
```
LOVE
MOVIE
```

**Output**
```
2
```

---

## What this problem really is

Minimum operations to turn string `A` into string `B` using insert, delete, or replace of a single character (Levenshtein distance).

## What to use

| Tool | Why |
|------|-----|
| `dp[n+1][m+1]` of `int` | `dp[i][j]` = edit distance between first `i` chars of `A` and first `j` of `B` |
| Three transitions | Insert / delete / replace (or match) cover every last operation |
| `O(n*m)` table | `n, m <= 5000` → about `25e6` cells — intended limit |

## Approach

**Root idea:** after aligning prefixes `A[0..i)` and `B[0..j)`, the last action that finished this alignment is one of: insert into `A`-side, delete from `A`-side, or replace/match the last pair of characters. Take the cheapest.

**Mental picture:** fill a grid. Row `i` = “used `i` letters of `A`”, column `j` = “used `j` letters of `B`”. Cell `(i,j)` is the cheapest way to make those two prefixes equal in edits.

**Data structures**

- Strings `A`, `B` (1-index or 0-index carefully).
- 2D array `dp[0..n][0..m]` (or two rolling rows of length `m+1` to save memory).

**Recurrence**

```
dp[0][0] = 0
dp[i][0] = i          // delete all i chars of A
dp[0][j] = j          // insert all j chars of B

dp[i][j] = min(
  dp[i-1][j] + 1,                                    // delete A[i]
  dp[i][j-1] + 1,                                    // insert B[j]
  dp[i-1][j-1] + (A[i] == B[j] ? 0 : 1)              // match or replace
)
```

**Steps**

1. Read `A`, `B`; let `n = A.size()`, `m = B.size()`.
2. Init borders as above.
3. Fill the table in increasing `i`, then `j`.
4. Answer is `dp[n][m]`.

## Walkthrough (on a sample)

`A = LOVE` (`n=4`), `B = MOVIE` (`m=5`).

Borders: `dp[i][0]=i`, `dp[0][j]=j`.

Focus on the end:

| step | meaning | cost idea |
|------|---------|-----------|
| replace `L→M` | prefixes `M` vs `M` | 1 |
| keep `OVE` vs `OV`… | match `O`,`V` | still 1 |
| insert `I` before `E` | … | 2 |
| match `E` | done | **2** |

One optimal sequence: `LOVE → MOVE → MOVIE`. DP finds `dp[4][5] = 2` without listing sequences.

## Pitfalls

- **Off-by-one indexing** when mixing 0-based strings with 1-based DP.
- **`n*m = 25e6`** — fine in C++; Python needs a tight loop / pypy.
- **Forgetting borders** — empty-to-nonempty distance is length, not `0`.
- Rolling-row version: update carefully so `dp[j-1]` still means previous column of current row.

## Complexity

- Time: `O(n * m)`  
- Space: `O(n * m)`, or `O(min(n,m))` with rolling rows

## Key takeaway

**Edit distance = grid DP.** Each cell is `min(delete, insert, replace/match)` from the three neighboring past cells.
