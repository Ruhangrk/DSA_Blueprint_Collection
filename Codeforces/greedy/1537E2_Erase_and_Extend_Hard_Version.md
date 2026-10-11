# [1537E2 — Erase and Extend (Hard Version)](https://codeforces.com/problemset/problem/1537/E2)

**Platform:** Codeforces
**Source:** Codeforces Round #726 (Div. 2)
**Tags:** binary search, data structures, greedy, hashing, string suffix structures, strings, two pointers
**Difficulty / Rating:** 2200
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round #726 (Div. 2) Editorial (Problem E2 section)](https://codeforces.com/blog/entry/91381)

---

## Problem Statement

You have a string `s`. Two operations are allowed:

- Delete the last character of the string.
- Duplicate the string: `s := s + s`.

Each operation may be used any number of times, including none.

Find the lexicographically smallest string of length exactly `k` that can be obtained.

A string `a` is lexicographically smaller than `b` if `a` is a proper prefix of `b`, or at the first position where they differ, `a` has a letter earlier in the alphabet.

### Input

The first line contains `n` and `k` (`1 <= n, k <= 5e5`), the length of `s` and the desired length.

The second line contains `s`, a string of `n` lowercase letters.

### Output

Print the lexicographically smallest string of length `k` that can be obtained.

### Constraints

- `1 <= n, k <= 5e5`
- `s` consists of lowercase English letters

### Examples

Input:

```
8 16
dbcadabc
```

Output:

```
dbcadabcdbcadabc
```

Input:

```
4 5
abcd
```

Output:

```
aaaaa
```

### Note

In the first test, it is optimal to duplicate once: `"dbcadabc"` becomes `"dbcadabcdbcadabc"`.

In the second test, delete the last three characters to get `"a"`, duplicate to `"aa"`, `"aaaa"`, `"aaaaaaaa"`, then delete down to length `5`.

---

## What this problem really is

Deleting from the end and duplicating means the reachable strings are always a prefix of `s` repeated, cut to length `k`. So the answer is the best among "prefix of `s` of length `m`, repeated forever, read the first `k` characters". The question is which prefix length `m` gives the smallest result, and we can find it in one pass over `s` instead of trying every prefix.

## What to use

| Tool | Why |
|------|-----|
| Candidate prefixes | The result is always some prefix `s[0..m-1]` repeated. Only prefix lengths need to be considered. |
| One left-to-right scan keeping the best prefix | When a new position beats or loses to the current candidate, the decision is final. |
| Z-function of `s` | Answers "where do two repeated patterns first differ" in `O(1)` for the tie case. |

## Approach

**Root idea:** Keep a current best prefix length `cur` (initially `1`). Scan positions `i = 1, 2, ...` and compare `s[i]` with the character the current best repetition has at position `i`, which is `s[i % cur]`.

- If `s[i] > s[i % cur]`, the current best repetition is already smaller than anything that uses a longer prefix. Stop: the answer is the current best.
- If `s[i] < s[i % cur]`, the prefix `s[0..i]` (length `i + 1`) is a strictly better repetition. Set `cur = i + 1`.
- If the characters are equal, the decision depends on deeper structure. Compare the two infinite repetitions using the Z-function of `s`, which finds the first mismatch quickly. If the longer prefix wins, set `cur = i + 1`; otherwise keep `cur`.

At the end, output the first `k` characters of the repetition of `s[0..cur-1]`.

**Mental picture:** Picture the best candidate as a rubber stamp pattern `s[0..cur-1]` pressed over and over along an infinite strip. Walk along `s`. Where `s` matches the stamp, carry on. Where `s` is lower than the stamp, the new prefix ends up with a better stamp, so replace the stamp with the prefix seen so far. Where `s` is higher, the stamp is already the best possible, so stop.

**Why stopping is safe:** Any longer candidate that agrees with the stamp up to position `i` and then has a larger character cannot beat the current stamp, and every later candidate starts with the same `s` prefix, so none can beat it either.

**Why the tie case needs the Z-function:** When `s[i] = s[i % cur]`, two candidate strings `AB...` and `BA...` (where the split depends on the two prefix lengths) either coincide or first differ at a position that can be read off the Z-array. Each tie is resolved in `O(1)`, so the whole scan is linear.

**Data structures:**
- The string `s`.
- `cur`, the length of the best prefix so far.
- The Z-array of `s`, used only in the tie case.

## Walkthrough (on a sample)

Test 1: `s = dbcadabc`, `n = 8`, `k = 16`. Start with `cur = 1` (stamp `d`).

- `i = 1`: `s[1] = b`, `s[1 % 1] = d`. `b < d`, so `cur = 2` (stamp `db`).
- `i = 2`: `s[2] = c`, `s[2 % 2] = d`. `c < d`, so `cur = 3` (stamp `dbc`).
- `i = 3`: `s[3] = a`, `s[3 % 3] = d`. `a < d`, so `cur = 4` (stamp `dbca`).
- `i = 4`: `s[4] = d`, `s[4 % 4] = d`. Equal. Compare the infinite repetitions of stamp `dbca` and candidate `dbcad`. They agree on the first 4 characters, then the stamp has `b` at position 5 while the candidate has `d`. The stamp is smaller, so keep `cur = 4`.
- `i = 5`: `s[5] = a`, `s[5 % 4] = b`. `a < b`, so `cur = 6` (stamp `dbcada`).
- `i = 6`: `s[6] = b`, `s[6 % 6] = d`. `b < d`, so `cur = 7`.
- `i = 7`: `s[7] = c`, `s[7 % 7] = d`. `c < d`, so `cur = 8` (stamp `dbcadabc`).
- The scan ends. Output the first `16` characters of the repetition of `dbcadabc`: `dbcadabcdbcadabc`. Matches the sample.

Test 2: `s = abcd`, `k = 5`. `cur = 1` (stamp `a`). At `i = 1`, `s[1] = b > a`, so stop. Output `aaaaa`. Matches the sample.

## Pitfalls

- **Forgetting that only prefixes matter:** Any other string is not reachable in this form, so do not consider substrings starting elsewhere.
- **Stopping too early on equal characters:** Equal characters do not decide anything; the tie check must run before any conclusion.
- **Using lexicographic comparison on the full infinite strings:** Compare only up to `k` characters, since the output is cut to `k`.
- **Shorter-is-smaller rule:** When one string is a proper prefix of another, the shorter one is smaller. Keep this in mind when comparing candidates of different lengths.
- **Output length:** Always print exactly `k` characters, even when `k` is larger than `n`.

## Complexity

- Time: `O(n + k)` with the Z-function, from one scan of `s` and one output pass.
- Space: `O(n)` for the Z-array.

## Key takeaway

When the answer is a repeated prefix of a string, scan once while keeping the best prefix length; each new character either settles the choice or is a tie resolved in constant time by a Z-array lookup.
