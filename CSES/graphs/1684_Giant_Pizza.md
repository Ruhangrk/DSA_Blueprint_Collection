# [1684 — Giant Pizza](https://cses.fi/problemset/task/1684)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, 2-SAT, SCC  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Uolevi's family is going to order a large pizza and eat it together. A total of `n` family members will join the order, and there are `m` possible toppings. The pizza may have any number of toppings.

Each family member gives two wishes concerning the toppings of the pizza. The wishes are of the form "topping `x` is good/bad". Your task is to choose the toppings so that at least one wish from everybody becomes true (a good topping is included in the pizza or a bad topping is not included).

### Input

The first input line has two integers `n` and `m`: the number of family members and toppings. The toppings are numbered `1, 2, ..., m`.

After this, there are `n` lines describing the wishes. Each line has two wishes of the form `"+ x"` (topping `x` is good) or `"- x"` (topping `x` is bad).

### Output

Print a line with `m` symbols: for each topping `"+"` if it is included and `"-"` if it is not included. You can print any valid solution.

If there are no valid solutions, print `"IMPOSSIBLE"`.

### Constraints

- `1 <= n, m <= 1e5`
- `1 <= x <= m`

### Examples

**Input**
```
3 5
+ 1 + 2
- 1 + 3
+ 4 - 2
```

**Output**
```
- + + + -
```

---

## What this problem really is

Each person gives a clause `(wish1 OR wish2)`. Each topping is a boolean (in / out). Satisfy all clauses → classic **2-SAT**.

## What to use

| Tool | Why |
|------|-----|
| Implication graph | `(A or B)` ↔ `¬A ⇒ B` and `¬B ⇒ A` |
| SCC (Kosaraju/Tarjan) | 2-SAT solvable iff no variable and its negation share an SCC |
| Condensation order | Assign truth by SCC reverse-topo |

## Approach

**Root idea:** encode wishes as literals. `+x` = variable `x` true; `-x` = `x` false. Each member’s two wishes form one OR-clause. Build implications, compute SCCs, assign values.

**Mental picture:** if you refuse one wish, you are forced to honor the other. Follow forced arrows; a contradiction is “must be both in and out.”

**Variable indexing:** for topping `i`, use nodes `i` (true) and `i+m` (false), or `2i` / `2i+1`.

**Steps**

1. For clause `(a or b)`, add edges `¬a → b` and `¬b → a`.
2. SCC on the `2m` nodes.
3. If `scc[x] == scc[¬x]` for any topping → `IMPOSSIBLE`.
4. Else set `x` true iff `scc[x] > scc[¬x]` (or the reverse convention matching your topo orientation — be consistent).
5. Print `+`/`-` for each topping.

## Walkthrough (on a sample)

Clauses roughly: `(1 or 2)`, `(¬1 or 3)`, `(4 or ¬2)`. One satisfying assignment yields toppings like **`- + + + -`**.

## Pitfalls

- Wrong implication edges for negative literals.
- Same SCC for `x` and `¬x` must be caught.
- Output format: `m` signs separated by spaces (as sample).

## Complexity

- Time: `O(n + m)` on the implication graph size `O(n + m)` edges / `O(m)` vars  
- Space: `O(n + m)`

## Key takeaway

**“At least one of two wishes” = 2-SAT.** Implication graph + SCC; contradict if `x` and `¬x` together; otherwise assign by component order.
