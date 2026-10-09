# [2108 — Substring Order I](https://cses.fi/problemset/task/2108)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** suffix array, LCP, k-th distinct substring  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a string of length `n`. If all of its distinct substrings are ordered lexicographically, what is the `k` th smallest of them?

### Input

The first input line has a string of length `n` that consists of characters `a`–`z`.

The second input line has an integer `k`.

### Output

Print the `k` th smallest distinct substring in lexicographical order.

### Constraints

- `1 <= n <= 1e5`
- `1 <= k <= n*(n+1)/2`
- It is guaranteed that `k` does not exceed the number of distinct substrings.

### Examples

**Input**
```
babaacbaab
10
```

**Output**
```
aba
```

Explanation: The 10 smallest distinct substrings in order are `a`, `aa`, `aab`, `aac`, `aacb`, `aacba`, `aacbaa`, `aacbaab`, `ab`, and `aba`.

---

## What this problem really is

Find the `k`-th string in the sorted list of **unique** substrings of `s`.

## What to use

| Tool | Why |
|------|-----|
| Suffix array | Suffixes sorted ⇒ their prefixes enumerate substrings in lex order |
| LCP array | Skip prefixes already listed by the previous suffix |
| Suffix automaton + subtree sizes | Alternative: walk SAM edges in char order using distinct-path counts |

## Approach

**Root idea:** in SA order, suffix `sa[i]` introduces exactly the new distinct prefixes of lengths `lcp[i]+1 .. n-sa[i]` (everything shorter was already contributed by an earlier suffix). Walk suffixes left to right, subtracting these new counts from `k` until you land inside one suffix; then take the prefix of the needed length.

**Mental picture:** sorted tails of the string. Each tail adds a streak of longer and longer prefixes that nobody before has shown.

**Steps (SA)**

1. Build `sa` and `lcp` (`lcp[0] = 0` for the first suffix).
2. `prev = 0` (shared with previous).
3. For `i = 0 .. n-1`:
   - `fresh = (n - sa[i]) - prev` new distinct prefixes.
   - If `k <= fresh`: answer = `s.substr(sa[i], prev + k)`; stop.
   - Else `k -= fresh`; `prev = lcp[i]` (for next iteration use `lcp` between `sa[i]` and `sa[i+1]` — match your LCP indexing carefully).
4. Print the answer.

**SAM alternative:** `dp[v]` = number of distinct substrings in the DAG from state `v` (including empty at `v`). Then from the root, try letters `a..z`; if `dp[child] >= k` go there (append letter, `k--` for the nonempty path ending here), else `k -= dp[child]`.

## Walkthrough (on a sample)

For `babaacbaab`, after nine smaller distinct substrings, the 10th fresh prefix reached is `aba`.

## Pitfalls

- Counting with multiplicity (that is Order II).
- Off-by-one on `lcp` indexing (`lcp` between `sa[i-1]` and `sa[i]`).
- Building the full sorted list of substrings — memory/time explosion.

## Complexity

- Time: `O(n log n)` SA + `O(n)` scan (answer length `O(n)`)  
- Space: `O(n)`

## Key takeaway

**k-th distinct substring = walk SA, each suffix adds lengths `lcp+1 .. suffix_len`** — stop when `k` falls in that range.
