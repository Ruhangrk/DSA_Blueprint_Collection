# [2165C — Binary Wine](https://codeforces.com/problemset/problem/2165/C)

**Platform:** Codeforces
**Source:** Codeforces Round 1064 (Div. 1, Div. 2)
**Tags:** bitmasks, greedy, math
**Difficulty / Rating:** 2000
**Time limit:** 2 seconds
**Memory limit:** 512 megabytes
**Editorial:** [Codeforces Round 1064 Editorial (Problem C section)](https://codeforces.com/blog/entry/148452)

---

## Problem Statement

You are given `n` integers `a[1], a[2], ..., a[n]`, all within the range `[0, 2^30)`.

You can spend `1` coin to increase any `a[i]` by `1`. You can perform this operation any number of times.

You need to solve `q` queries. For each query, you are given an integer `c`, also in the range `[0, 2^30)`. You would like it if there exists a sequence `b` of length `n` with the following properties:

- For every `1 <= i <= n`, `0 <= b[i] <= a[i]`.
- `b[1] ^ b[2] ^ ... ^ b[n] = c`, where `^` denotes the bitwise XOR operation.

Please calculate the minimum number of coins you will have to spend, such that there exists a suitable `b`.

The queries are independent, meaning that any operations you perform on the sequence `a` will not impact future queries.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each test case consists of two integers `n`, `q` (`1 <= n <= 5e5`, `1 <= q <= 5e4`), the length of sequence `a` and the number of queries.

The second line of each test case contains `n` integers `a[1], ..., a[n]` (`0 <= a[i] < 2^30`).

Each of the next `q` lines contains a single integer `c` (`0 <= c < 2^30`), the target XOR.

It is guaranteed that the sum of `n` over all test cases does not exceed `5e5`.

It is guaranteed that the sum of `q` over all test cases does not exceed `5e4`.

### Output

For each query, output a single integer: the minimum coins you will have to spend, such that there exists a suitable `b`.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 5e5`, `1 <= q <= 5e4`
- `0 <= a[i], c < 2^30`
- Sum of `n` `<= 5e5`, sum of `q` `<= 5e4`

### Examples

Input:

```
4
2 1
5 7
9
3 1
9 9 8
24
6 4
1 1 4 5 1 4
10
20
30
40
1 1
0
0
```

Output:

```
1
7
3
11
16
31
0
```

### Note

In the first test case, we spend `1` coin to increase `a[2]` by `1`, resulting in sequence `[5, 8]`. A suitable `b` would be `[1, 8]`. It can be shown one cannot spend less than `1` coin to achieve the objective.

In the second test case, we can spend `7` coins to increase `a[1]` by `7`, resulting in sequence `[16, 9, 8]`. A suitable `b` would be `[16, 9, 1]`.

---

## What this problem really is

Each `a[i]` is a cap: it lets you pick any value from `0` up to `a[i]`. We need the XOR of the picked values to equal `c`. Raising caps costs coins. The question is: what is the cheapest set of cap increases that makes some choice of values XOR to `c`?

## What to use

| Tool | Why |
|------|-----|
| Greedy from the highest bit down | Higher bits dominate the XOR result, so decide them first. |
| Sorting `a` in descending order | Only the largest caps can matter, because every bit of `c` needs at most one "fixing" element. |
| Keeping at most about 32 largest values | Values are below `2^30`, so at most 30 bit positions exist; extra elements never help. |
| Per-query copy of the top values | Queries are independent, so each query works on a fresh copy of the top caps. |

## Approach

**Root idea:** Look at the bits of `c` from high to low. At each bit `i`, count how many current caps still have bit `i` available (set in the remaining value). Compare that count with bit `i` of `c`:

- If the count is larger than `c`'s bit, one spare `1` at this position lets the lower bits be fixed freely. Stop: no more coins are needed.
- If the count is equal, use those `1`s to match `c`'s bit, remove bit `i` from every value, and move on to the next bit.
- If the count is smaller (so it is `0` while `c`'s bit is `1`), no cap has this bit. Spend coins on exactly one cap: raise the largest value up to `2^i`. That cap now supplies bit `i` and has nothing left below it. Remove it from consideration and move on.

**Mental picture:** Think of each cap as a bucket of water that can be poured down to any level between `0` and its size. The high bits of `c` must be filled first. If a high bit is missing, you top up one bucket until it reaches exactly that bit's level, which costs the gap. Once one bucket supplies the high bit, the others can freely adjust the lower bits, so you only ever upgrade one bucket per missing bit.

**Why one element per missing bit:** If you raised two buckets to fix the same missing bit, you could instead raise just one of them to the bit's level and the other would already be enough to cover all lower bits. So at each missing bit, a single upgrade is optimal. The chosen bucket should be the largest one, because it needs the smallest top-up.

**Why only the top values matter:** Every upgrade fixes one bit position, and there are at most 30 positions. The chosen bucket is always among the largest values. So only the top 32 values need to be checked.

**Data structures:**
- `a` sorted in descending order, read once per test.
- `b[1..32]`: a per-query copy of the largest values; entries are zeroed or have bits cleared as the bit scan proceeds.
- A running answer `ans` (64-bit), summing the top-up costs.

**Short steps (per query):**
1. Copy the top 32 values into `b`.
2. For `i` from 29 down to 0: count bit `i` across `b`, compare to bit `i` of `c`, and apply the rule above.
3. Output `ans`.

## Walkthrough (on a sample)

Test 2: `a = [9, 9, 8]`, `c = 24` (binary `11000`, bits 4 and 3 set).

- Bits 29 to 5: count is `0`, `c` bit is `0`. Equal, nothing to do.
- Bit 4: count is `0` (all values are below 16), `c` bit is `1`. Missing. Pick largest value `9` and raise it to `16`. Cost `16 - 9 = 7`. Set that value to `0`. Now `b = [0, 9, 8]`.
- Bit 3: values `9` (`1001`) and `8` (`1000`) both have bit 3. Count is `2`, `c` bit is `1`. Count is larger, so stop. Answer `7`.

Test 3 (query `c = 10`, binary `1010`): `a` sorted is `[5, 4, 4, 1, 1, 1]`. At bit 3, no value reaches `8`, and `c` needs it, so raise `5` to `8` at cost `3`. At bit 2, the remaining values `4` and `4` give count `2`, but `c` bit 2 is `0`, so count is larger. Stop. Answer `3`.

## Pitfalls

- **Comparing with the wrong bit scan:** Scan from the high bit down. Scanning low to high gives wrong answers because high bits decide the result.
- **Upgrading more than one value for the same missing bit:** One upgrade is enough; extra upgrades cost more.
- **Choosing a small value to raise:** Raising the largest value is cheapest because the top-up is `2^i - value`.
- **Forgetting to clear bit `i` on equal counts:** Those `1`s are used to match `c`, so they must not be counted again at lower bits.
- **Overflow on the cost:** Costs can reach around `2^30` per bit; accumulate in 64-bit.
- **Per-query state leaks:** Each query must start from the original sorted values.

## Complexity

- Time: sorting is `O(n log n)` per test. Each query costs `O(30 * 32)` for the bit scan, so `O(q * 30 * 32)` overall, which is small.
- Space: `O(n)` for the array, plus `O(32)` per query.

## Key takeaway

When a bitwise target must be met by bounded values with upgrade costs, decide the highest bits first, and fix each missing bit with one upgrade to the largest available value.
