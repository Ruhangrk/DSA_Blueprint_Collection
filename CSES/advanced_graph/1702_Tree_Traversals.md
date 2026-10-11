# [1702 — Tree Traversals](https://cses.fi/problemset/task/1702)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** binary tree, recursion, traversals  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are three common ways to traverse the nodes of a binary tree:

- Preorder: First process the root, then the left subtree, and finally the right subtree.
- Inorder: First process the left subtree, then the root, and finally the right subtree.
- Postorder: First process the left subtree, then the right subtree, and finally the root.

There is a binary tree of `n` nodes with distinct labels. You are given the preorder and inorder traversals of the tree, and your task is to determine its postorder traversal.

### Input

The first input line has an integer `n`: the number of nodes. The nodes are numbered `1,2,...,n`.

After this, there are two lines describing the preorder and inorder traversals of the tree. Both lines consist of `n` integers.

You can assume that the input corresponds to a binary tree.

### Output

Print the postorder traversal of the tree.

### Constraints

- `1 <= n <= 1e5`

### Examples

**Input**
```
5
5 3 2 1 4
3 5 1 2 4
```

**Output**
```
3 1 4 2 5
```

---

## What this problem really is

Recover a binary tree’s shape from preorder + inorder, then emit postorder — without building an explicit pointer tree if you do not want to.

## What to use

| Tool | Why |
|------|-----|
| Preorder root | First unused preorder value is the current subtree root |
| Inorder split | Root’s position splits left / right subtrees |
| Index map | `pos[label]` in inorder → `O(1)` split; needed for `n=1e5` |
| Recursion / stack | Emit left, right, then root for postorder |

## Approach

**Root idea:** preorder lists roots first; inorder tells you which labels fall left vs right of each root. Recurse on those ranges; postorder is “children first, root last.”

**Mental picture:** the preorder sequence is a stream of roots. Each root claims a contiguous inorder segment. Walk the stream left-to-right while carving those segments.

**How that becomes the method**

1. Build `pos[value] =` index in the inorder array.
2. Keep a global index `preIdx` into preorder (start `0`).
3. Function `build(inL, inR)` for inorder range `[inL, inR)`:
   - if empty, return;
   - `root = preorder[preIdx++]`;
   - `m = pos[root]`;
   - recurse left `build(inL, m)`, right `build(m+1, inR)`;
   - append `root` to the answer (postorder).
4. Call `build(0, n)` and print the answer.

For `n=1e5`, either increase recursion limit / use an explicit stack, or ensure the tree is not a pathological chain that overflows — iterative simulation of the same logic is safer in some languages; in C++ default stack often handles typical trees but skewed trees can be deep — an iterative approach or `boost`/careful stack size may be needed. Structuring as “compute sizes from inorder span and advance `preIdx` by subtree size” also works iteratively.

**Data structures:** `pre[]`, `in[]`, `pos[n+1]`, answer vector; optional node structs.

## Walkthrough (on a sample)

Preorder: `5 3 2 1 4`  
Inorder: `3 5 1 2 4`

- Root `5`; inorder left of `5` is `[3]`, right is `[1,2,4]`.
- Left subtree: preorder next `3` → leaf `3`. Post emits `3`.
- Right subtree root `2`; inorder within `[1,2,4]`: left `[1]`, right `[4]`.
  - Left: `1` → emit `1`.
  - Right: `4` → emit `4`.
  - Emit `2`.
- Emit `5`.

Postorder: `3 1 4 2 5`.

## Pitfalls

- `O(n^2)` linear search for root in inorder → TLE at `1e5`.
- Mixing up left/right ranges.
- Deep recursion on a skewed tree (`n=1e5` chain).

## Complexity

- Time: `O(n)`
- Space: `O(n)` for maps / recursion stack

## Key takeaway

Preorder names the root; inorder splits the range — recurse left/right then output the root for postorder.
