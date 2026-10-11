# [1087 — Shortest Subsequence](https://cses.fi/problemset/task/1087)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** greedy, strings, subsequences  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a DNA sequence consisting of characters `A`, `C`, `G`, and `T`.

Your task is to find the shortest DNA sequence that is **not** a subsequence of the original sequence.

### Input

The only input line contains a DNA sequence with `n` characters.

### Output

Print the shortest DNA sequence that is not a subsequence of the original sequence. If there are several solutions, you may print any of them.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
ACGTACGT
```

**Output**
```
AAA
```

---

## What this problem really is

Over alphabet `{A,C,G,T}`, output a shortest string that does **not** appear as a subsequence of `s`.

## What to use

| Tool | Why |
|------|-----|
| Greedy layer / “full set” scan | Each time all 4 letters have appeared since the last cut, you can force one output character |
| Remaining letter at the end | Extends the answer by one char that breaks subsequence matching |

## Approach

**Root idea:** a string fails to be a subsequence when matching it greedily left-to-right in `s` gets stuck. Build a short witness by repeatedly recording a character that completes a “seen all letters” block.

**Mental picture:** walk through `s` crossing off letters from a checklist of four. When the checklist empties, you have survived one full alphabet layer as a subsequence — append the letter that finished the layer to your answer and reset the checklist. At the end, append any letter still unchecked (or any letter if the checklist is full — need one more that cannot be matched).

**How that becomes the method**

1. `need = {A,C,G,T}`; `ans` empty.
2. For each character `c` in `s`:
   - erase `c` from `need` (if present);
   - if `need` is empty: append `c` to `ans`, reset `need = {A,C,G,T}`.
3. After the scan: append any character that is still in `need` (if `need` is empty, append any letter, e.g. `A` — but typically `need` is nonempty).  
   More carefully: always append one char from the current `need` after the loop (if `need` empty, the last reset happened at end — append any).
4. Print `ans`.

This produces a shortest possible length (equal to one plus the maximum number of disjoint “cover all letters” layers in a subsequence sense).

**Data structures:** a tiny set / bitmask of size 4; output string.

## Walkthrough (on a sample)

`ACGTACGT`: first `ACGT` empties the set at `T` → append `T`, reset. Then `ACGT` empties at second `T` → append `T`, reset. End with full `need` again… actually after two full layers, `need` is full `{A,C,G,T}`; append e.g. `A` → `TTA` or similar. Sample shows `AAA`, also valid length 3.

## Pitfalls

- Confusing subsequence with substring.
- Alphabet only four letters — do not use general string automata for `n=1e6` if avoidable.
- Forgetting the final extra character.

## Complexity

- Time: `O(n)`
- Space: `O(n)` for the answer (at most `n/1 + 1` chars, tiny in practice)

## Key takeaway

Shortest missing subsequence over a tiny alphabet → greedy “clear all letters, append, reset,” then one final letter.
