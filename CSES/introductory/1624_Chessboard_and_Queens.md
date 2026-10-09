# [1624 — Chessboard and Queens](https://cses.fi/problemset/task/1624)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** backtracking, N-queens  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to place eight queens on a chessboard so that no two queens are attacking each other. As an additional challenge, each square is either free or reserved, and you can only place queens on the free squares. However, the reserved squares do not prevent queens from attacking each other.

How many possible ways are there to place the queens?

### Input

The input has eight lines, and each of them has eight characters. Each square is either free (`.`) or reserved (`*`).

### Output

Print one integer: the number of ways you can place the queens.

### Examples

**Input**
```
........
........
..*.....
........
........
.....**.
...*....
........
```

**Output**
```
65
```

---

## What this problem really is

Count solutions to the **8-queens** problem on a board where some squares are blocked (`*`) — queens may only sit on `.`, but attacks pass through reserved squares.

## What to use

| Tool | Why |
|------|-----|
| Backtracking row by row | Place one queen per row (standard N-queens) |
| Column / diagonal masks | `col`, `diag1 = r+c`, `diag2 = r-c+7` occupied flags |
| Board check | Skip `*` when trying a cell |

## Approach

**Root idea:** place exactly one queen in each row (necessary for 8 non-attacking queens on 8×8). In row `r`, try each free column `c` not attacked; recurse to row `r+1`. When `r == 8`, count +1.

**Mental picture:** drop queens row by row onto safe open squares, never sharing a file or diagonal.

**Data structures**

- `char board[8][8]`.
- Booleans `usedCol[8]`, `usedDiag1[15]`, `usedDiag2[15]`.
- Counter `ans`.

**Steps**

1. Read the board.
2. `dfs(row)`: if `row == 8`, `ans++`; else for `c = 0..7`: if `board[row][c]=='.'` and column/diags free, mark, `dfs(row+1)`, unmark.
3. Print `ans`.

## Walkthrough (on a sample)

Empty 8×8 has 92 solutions; with a few `*` squares some placements die → sample **65**.

## Pitfalls

- Treating `*` as blocking attacks — they don’t; only placement is forbidden.
- Allowing two queens in the same row.
- Off-by-one on diagonal index `r - c + 7`.

## Complexity

- Time: pruned 8-queens search (tiny)  
- Space: `O(1)` / `O(8)` recursion

## Key takeaway

**Queens on a masked board = N-queens backtracking** that only tries `.` squares; reserved cells don’t stop attacks.
