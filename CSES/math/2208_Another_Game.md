# [2208 — Another Game](https://cses.fi/problemset/task/2208)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** game theory, pairing strategy  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` heaps of coins and two players who move alternately. On each move, a player selects some of the nonempty heaps and removes one coin from each heap. The player who removes the last coin wins the game.

Your task is to find out who wins if both players play optimally.

### Input

The first input line contains an integer `t`: the number of tests. After this, `t` test cases are described:

The first line contains an integer `n`: the number of heaps.

The next line has `n` integers `x[1], x[2], ..., x[n]`: the number of coins in each heap.

### Output

For each test case, print `"first"` if the first player wins the game and `"second"` if the second player wins the game.

### Constraints

- `1 <= t <= 2e5`
- `1 <= n <= 2e5`
- `1 <= x[i] <= 1e9`
- the sum of all `n` is at most `2e5`

### Examples

**Input**
```
3
3
1 2 3
2
2 2
4
5 5 4 5
```

**Output**
```
first
second
first
```

---

## What this problem really is

Each turn: pick any nonempty **subset** of heaps and decrement each by 1. Last coin(s) win. Not standard Nim.

## What to use

| Tool | Why |
|------|-----|
| Parity insight | Odd heaps are the only “tokens” that matter |
| Pairing / mirroring | If all heaps even, second copies first’s subset forever |
| One check for any odd `x[i]` | First wins iff at least one heap is odd |

## Approach

**Root idea:** if every heap size is even, second can always answer with the **same subset** of heaps the first just touched — after first’s move those heaps become odd; second makes them even again. This continues until second takes the last coins.

If some heap is odd, first can subtract 1 from **every currently odd heap** in one move, leaving an all-even position — a losing position for the opponent under the strategy above. So first wins.

**Mental picture:** each heap’s parity is a light. First’s winning shot flips all “on” lights off in one go. If all lights are already off (all even), first is forced to turn some on, and second turns them off again.

**Steps**

1. Scan the heaps.
2. If any `x[i]` is odd → print `first`.
3. Else print `second`.

## Walkthrough (on a sample)

- `1 2 3`: odds present → first (e.g. hit heaps 1 and 3 → leave `0 2 2`).  
- `2 2`: all even → second.  
- `5 5 4 5`: odds present → first.

## Pitfalls

- Treating it as XOR of sizes (ordinary Nim) — wrong move set.  
- Thinking you may remove only from one heap.  
- Ignoring that selecting **multiple** heaps in one turn is allowed.

## Complexity

- Time: `O(sum n)`  
- Space: `O(1)`

## Key takeaway

**Decrement-any-subset game: first wins iff some heap has odd size** — otherwise second mirrors.
