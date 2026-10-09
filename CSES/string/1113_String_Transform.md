# [1113 — String Transform](https://cses.fi/problemset/task/1113)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** BWT, inverse Burrows-Wheeler  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider the following string transformation:

1. append the character `#` to the string (we assume that `#` is lexicographically smaller than all other characters of the string)
2. generate all rotations of the string
3. sort the rotations in increasing order
4. based on this order, construct a new string that contains the last character of each rotation

For example, the string `babc` becomes `babc#`. Then, the sorted list of rotations is `#babc`, `abc#b`, `babc#`, `bc#ba`, and `c#bab`. This yields a string `cb#ab`.

### Input

The only input line contains the transformed string of length `n+1`. Each character of the original string is one of `a`–`z`.

### Output

Print the original string of length `n`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
cb#ab
```

**Output**
```
babc
```

---

## What this problem really is

Invert the **Burrows–Wheeler transform (BWT)** of `s + "#"`. Input is the last column `L` of the sorted-rotation matrix; recover the original `s` (without `#`).

## What to use

| Tool | Why |
|------|-----|
| LF-mapping | Maps a character in last column `L` to its matching occurrence in first column `F` |
| Stable sort of `L` | Produces `F` and the rank used by LF |
| Walk from `#` | Follow predecessors / successors around the cyclic string |

## Approach

**Root idea:** sorted rotations share the property that the first column `F` is just `L` sorted. The `i`-th occurrence of character `c` in `L` corresponds to the `i`-th occurrence of `c` in `F` (LF property). That pairs each row’s last char with the row where that char is first — i.e. the cyclic shift by one.

**Mental picture:** a matrix of all necklaces sorted. Last column is scrambled; first column is alphabetical. LF says “same beads match in order,” so you can hop row-to-row around the cycle and read the original string.

**Steps (inverse BWT)**

1. Let `L` be the input (length `m = n+1`).
2. Build `F` by sorting `L` (or counting sort over alphabet + `#`).
3. Compute `LF[j]`: if `L[j]` is the `t`-th copy of that character in `L`, then `LF[j]` = position of the `t`-th copy of that character in `F`.
4. Find `row` where `L[row] == '#'`.
5. Reconstruct by walking `m` steps: repeatedly output / collect `L[row]` then `row = LF[row]` (order depends on whether you build the string backward or forward — standard is recover `T` from end to start, skipping `#` at the end).
6. Print the `n` letters of the original string (drop `#`).

Counting-sort LF is `O(m)` over a small alphabet.

## Walkthrough (on a sample)

Input `cb#ab` is BWT of `babc#`. Inverse LF walk recovers `babc#` → output `babc`.

## Pitfalls

- Unstable matching of equal characters (must use occurrence ranks).
- Leaving `#` in the printed string.
- `O(m log m)` sort is fine; naive matrix rebuild is not.
- Wrong walk direction / off-by-one on where `#` sits.

## Complexity

- Time: `O(n)` with counting sort  
- Space: `O(n)`

## Key takeaway

**String Transform = BWT; invert with LF-mapping** — sort `L` to get `F`, match equal letters by rank, walk from `#`.
