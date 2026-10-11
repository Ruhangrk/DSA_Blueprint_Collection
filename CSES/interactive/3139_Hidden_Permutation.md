# [3139 — Hidden Permutation](https://cses.fi/problemset/task/3139)

**Platform:** CSES  
**Source:** CSES Problem Set — Interactive Problems  
**Tags:** interactive, sorting, comparisons  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a hidden permutation `a1, a2, ..., an` of integers `1, 2, ..., n`. Your task is to find this permutation.

To do this, you can ask questions: you can choose two indices `i` and `j` and you will be told if `a[i] < a[j]`.

### Interaction

This is an interactive problem. Your code will interact with the grader using standard input and output. You should start by reading a single integer `n`: the length of the permutation.

On your turn, you can print one of the following:

- `? i j`, where `1 <= i, j <= n`: ask if `a[i] < a[j]`. The grader will return `YES` if `a[i] < a[j]` and `NO` otherwise.
- `! a1 a2 ... an`: report the hidden permutation. Your program must terminate after this.

Each line should be followed by a line break. Flush after every print.

### Constraints

- `1 <= n <= 1000`
- you can ask at most `1e4` questions of type `?`

### Examples

```
3
? 3 2
NO
? 3 1
YES
! 3 1 2
```

Explanation: The hidden permutation is `[3, 1, 2]`.

---

## What this problem really is

Recover a hidden array that is a permutation by asking pairwise order between positions — i.e. sort the indices by hidden values using a comparison oracle.

## What to use

| Tool | Why |
|------|-----|
| Comparison sort on indices | `cmp(i,j) = query(? i j)` |
| `O(n log n)` sorts | Merge/heap/std::sort with custom cmp fits in `1e4` for `n=1000` (`~n log n ~ 10k`) |
| Rank → value | After sorting indices by increasing `a[i]`, assign values `1..n` |

## Approach

**Root idea:** you never see values, only order between positions. Sort the list of indices with the interactive comparator; the sorted order is increasing values, so assign `1..n` along that order.

**Mental picture:** `n` sealed boxes; you may ask which of two boxes is lighter. Weigh-sort them, then label lightest=`1`, …, heaviest=`n`.

**How that becomes the method**

1. Read `n`. Put indices `1..n` in a vector `p`.
2. Sort `p` with comparator that asks `? i j` and returns whether `a[i]<a[j]` (flush; read `YES`/`NO`).
3. Let `ans[p[k]] = k+1` (0-based `k`) for increasing order.
4. Print `!` and `ans[1..n]`.

Stay within ~`n log n` comparisons; avoid `O(n^2)` bubble if constants blow the `1e4` cap (strictly, `n=1000` merge sort ~ 10k — tight; prefer careful sort or insertion with binary search ~ `n log n`).

**Data structures:** vector of indices; answer array.

## Walkthrough (on a sample)

Comparisons establish order of positions by value; final print `3 1 2`.

## Pitfalls

- Not flushing.  
- Too many comparisons (`O(n^2)`).  
- Assigning ranks in the wrong direction (descending vs ascending).

## Complexity

- Queries: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Hidden permutation via pairwise `<` = sort indices with the oracle**, then write ranks.
