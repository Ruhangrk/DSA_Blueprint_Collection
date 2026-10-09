# [1091 — Concert Tickets](https://cses.fi/problemset/task/1091)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** multiset, binary search, greedy  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` concert tickets available, each with a certain price. Then, `m` customers arrive, one after another.

Each customer announces the maximum price they are willing to pay for a ticket, and after this, they will get a ticket with the nearest possible price such that it does not exceed the maximum price.

### Input

The first input line contains integers `n` and `m`: the number of tickets and the number of customers.

The next line contains `n` integers `h1, h2, ..., hn`: the price of each ticket.

The last line contains `m` integers `t1, t2, ..., tm`: the maximum price for each customer in the order they arrive.

### Output

Print, for each customer, the price that they will pay for their ticket. After this, the ticket cannot be purchased again.

If a customer cannot get any ticket, print `-1`.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= hi, ti <= 1e9`

### Examples

**Input**
```
5 3
5 3 7 8 5
4 8 3
```

**Output**
```
3
8
-1
```

---

## What this problem really is

Online queries: for each customer budget `t`, take the **largest remaining ticket price `<= t`**, or fail. Tickets are unique uses (multiset).

## What to use

| Tool | Why |
|------|-----|
| `multiset` (ordered) | `O(log n)` find + erase of one ticket |
| `upper_bound` / `lower_bound` | Locate first price `> t`, then step one left |
| Why not sort + two pointers once? | Customers are sequential and remove tickets — need dynamic structure |

## Approach

**Root idea:** keep all ticket prices in a balanced BST. For budget `t`, binary-search the successor of `t` and take the previous element — that is the best affordable ticket.

**Mental picture:** a price rack sorted low→high; customer points at “at most `t`”; you hand them the ticket just left of the first price that overshoots.

**Data structures**

- `multiset<int> tickets` with all `h[i]`.

**Per customer `t`**

1. `auto it = tickets.upper_bound(t);` — first price **strictly greater** than `t`.
2. If `it == tickets.begin()`, nothing `<= t` → print `-1`.
3. Else `--it`; print `*it`; `tickets.erase(it)` (erase that one occurrence).

Alternative: `multiset` with `greater<int>` and `lower_bound(t)` for “largest `<= t`” in one step — same idea.

## Walkthrough (on a sample)

Tickets `{3,5,5,7,8}`.

Customer `4`: `upper_bound(4)` → `5`; previous → **3**; erase `3`. Left `{5,5,7,8}`.

Customer `8`: `upper_bound(8)` → end; previous → **8**; erase. Left `{5,5,7}`.

Customer `3`: `upper_bound(3)` → `5`; previous would be begin if no `<=3` — begin is `5 > 3` → **`-1`**.

## Pitfalls

- Using `lower_bound(t)` and taking that element when it may be `> t`.
- `erase(value)` erases **all** equal prices — must erase by iterator.
- Calling `--begin()` when no ticket `<= t`.
- Sorting once and scanning without removals — wrong for later customers.

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n)`

## Key takeaway

**Largest available price `<= t` with deletions = multiset + `upper_bound(t)` then `--it`** (erase that iterator).
