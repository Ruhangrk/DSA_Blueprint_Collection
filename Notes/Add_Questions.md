# Agent Prompt — Add DSA / CP Questions

Copy everything below the line into a Cursor / Cascade chat when you want problems added to this repo.

---

## Mission

You are helping build **DSA_Blueprint_Collection**: a personal library of competitive programming and DSA problems.

Goal of every page: **build intuition** so the learner can solve *new* problems in the same pattern — not memorize solutions.

When the user asks to add question(s), follow this document exactly.

---

## Present Status 

You will add entire CSES set here, but fetch only 3 questions into 3 pages at a time. Make sure that quaity is never sacrified. High quality page is top priority. Before you select which 3 next questions to add, make sure those are already not present in this repo. 

---

## Repo layout

Platform folders already live at the **repo root** (`Codeforces/`, `Leetcode/`, `CSES/`, …). Do not wrap them in an extra `Questions/` folder.

```
Codeforces/            # or Leetcode/ | CSES/ | …
  <topic>/             # graphs | dp | greedy | trees | binary_search | …
    [<subtopic>/]      # ONLY if a major subcategory clearly applies
                       # e.g. dp/knapsack, graphs/shortest_path
                       # Do NOT invent tiny folders or rating-band folders
      <ID>_<Short_Title>.md
Notes/
  Add_Questions.md     # this prompt
```

**Naming**

- Codeforces: `Codeforces/graphs/500A_New_Year_Transportation.md`
- LeetCode: `Leetcode/graphs/200_Number_of_Islands.md`
- Use a short stable title with underscores.

**How many problems / batching**

- Default batch: **5 problems**, all from the **same topic** (same folder, e.g. all `CSES/dp/` or all `Codeforces/graphs/`).
- Same topic in one chat keeps pattern teaching consistent and quality easier to hold.
- **Never sacrifice quality for count.** If a problem needs more care, finish fewer well rather than pad to 5 with weak pages.
- If the user names a different count, follow that. If topic or count is unclear, ask before adding.
- Soft max per chat: **5**. Do not do 10 in one go.

**Language:** English only.

**Each file is standalone (mandatory)**

A reader may open **only one** question file. Never assume they read siblings in order.

- Do **not** write “as in Dice Combinations”, “same as problem I”, “contrast with II”, “still …”, “see previous file”, etc.
- Every page must explain its own root idea, mental picture, and method from scratch.
- Contrasts are fine **only if self-contained** (e.g. “if you swap the loop order you would count permutations instead — wrong here because …”) with no dependency on another markdown file.
- Generic pattern names (`unbounded knapsack`, `flood fill`) are OK; pointers to other repo pages are not.

**Notation (C++ style — mandatory)**

Write math and variables like C++ / competitive programming notes, **not** LaTeX.

| Avoid (messy) | Use instead |
|---------------|-------------|
| `\(...\)`, `\[...\]`, `$...$` | plain text + backticks |
| `\mathrm{dp}[s]`, `\le`, `\times`, `\ldots` | `dp[s]`, `<=`, `*`, `...` |
| `10^9+7`, `2 \cdot 10^5` | `1e9+7`, `2e5` |
| `x_1, a_i`, `\{1,\ldots,6\}` | `x1` / `x[i]` / `a[i]`, `{1..6}` |

Examples: `` `1 <= n <= 1e6` ``, `` `dp[s] = dp[s-1] + ... + dp[s-6]` ``, `` `i -> i + a[i]` ``.

Keep the official meaning of the statement; only change **notation**, not the problem itself.

---

## How to obtain the problem

1. **Pick / metadata**
   - Prefer platform MCP if available (Codeforces MCP, LeetCode MCP, etc.).
   - Codeforces MCP often gives **metadata only** (id, name, rating, tags, solvedCount) — not the full statement.
2. **Full statement**
   - Fetch the official problem page (or LeetCode MCP `get_problem` if it returns description).
   - Do **not** invent or paraphrase the statement. Keep meaning faithful; rewrite math into **C++-style notation** (see above).
3. **Editorial**
   - You **may** read the editorial / tutorial for accuracy.
   - If you find an editorial, **put its link** in the page header.
   - **Rewrite** the explanation in your own intuition-first words. Do **not** paste the editorial.

---

## What every question file MUST contain

### 1. Header (always)

- Title as markdown link to the **problem URL** (so the learner can open, code, and submit)
- Platform / source (contest name if known)
- Tags
- Difficulty / rating (as given by the platform)
- Time / memory limits when the platform lists them
- **Editorial link** when found (label it clearly)

### 2. Problem Statement (always — full)

- Complete statement
- Input / Output format
- Constraints
- All samples (input + output)
- Official notes / explanations of samples if present

### 3. Intuition sections (always — no code)

Teach the pattern. Structure exactly like this:

```markdown
## What this problem really is
## What to use
## Approach
## Walkthrough (on a sample)
## Pitfalls
## Complexity
## Key takeaway
```

**Section intent**

| Section | Purpose |
|--------|---------|
| What this problem really is | Strip story → core CS question (1–3 sentences) |
| What to use | DS / algo / technique + *why* (table or bullets) |
| Approach | Root idea first, then a clear mental picture, then short steps — see below |
| Walkthrough | Trace the approach on at least one sample so the picture becomes concrete |
| Pitfalls | Edge cases, common WA reasons, easy misreads |
| Complexity | Time and space, tied to constraints |
| Key takeaway | One memorable line for transfer to new problems |

**Approach — required shape (clarity over checklist)**

Do **not** dump only numbered formula steps. The reader must leave this section able to *see* why the method works.

1. **Root idea (one thing)** — the single insight the problem reduces to (e.g. “split on the last throw”, “follow the unique path”, “each flood fill is one room”). State it in plain language first.
2. **Mental picture** — a short concrete image or analogy the reader can visualize (line of length `s`, portal jumps, grid water fill, …). One tight paragraph or a small text diagram is enough.
3. **How that becomes the method** — recurrence / invariant / algorithm in words, tied back to the picture.
4. **Data structures (when the solution is algorithmic)** — name what you store (adj list, `dist[][]`, PQ, `dp[]`, visited, …) and **how each is used** in the method. Do not leave the reader with only a high-level story.
5. **Short steps** — only after the idea is clear; keep them minimal.

**Walkthrough** must dry-run the real method (state updates / PQ pops / DP fills), not only narrate the final answer path.

Dense but clear beats long and vague. No filler, no repeated restatements, no “just do DP” without the *why*.

### 4. Explicitly forbidden in question files

- **No solution code** (no C++/Python/Java blocks, no “reference implementation”)
- No pasted editorial text
- No spoilers that skip intuition (“just do X”) without saying *why*
- No messy padding (extra asides, redundant tables, or long digressions that do not teach the pattern)
- **No LaTeX** (`\(...\)`, `\[...\]`, `\le`, `\mathrm{...}`, etc.) — use C++-style backticks instead
- **No cross-file dependencies** — each question page must make sense alone

Pseudocode-level bullet steps are OK when they teach the algorithm. Full compilable code is not.

---

## Page template (copy this shape)

```markdown
# [<ID> — <Title>](<problem_url>)

**Platform:** …
**Source:** …
**Tags:** …
**Difficulty / Rating:** …
**Time limit:** …          <!-- omit if N/A -->
**Memory limit:** …        <!-- omit if N/A -->
**Editorial:** [<name>](<editorial_url>)   <!-- omit if none found -->

---

## Problem Statement

…

### Input
…
### Output
…
### Constraints
…
### Examples
…
### Note
…   <!-- if official note exists -->

---

## What this problem really is

…

## What to use

| Tool | Why |
|------|-----|
| … | … |

## Approach

**Root idea:** …

Mental picture / why it works: …

Steps:
1. …
2. …

## Walkthrough (on a sample)

…

## Pitfalls

- …
- …

## Complexity

- Time: …
- Space: …

## Key takeaway

…
```

---

## Quality bar (intuition-first)

Before finishing, check:

1. Could a reader who never saw this problem **recognize the pattern** on a new statement?
2. Does “What this problem really is” remove the story fluff?
3. Does “What to use” explain *why*, not only *what*?
4. Does **Approach** lead with one root idea + a mental picture the reader can imagine — not only formula steps?
5. Does Walkthrough make that picture concrete on a sample?
6. Do Pitfalls cover the traps that actually fail submissions?
7. Is the **problem link** correct and clickable?
8. Is there **zero code**, and no useless padding?
9. Is notation **C++ style** (backticks, `<=`, `1e9+7`, `dp[s]`) with **no LaTeX**?
10. Can someone who **only opened this file** understand Approach with zero other pages?
11. File path matches `<Platform>/<topic>/[subtopic/]<file>.md` at repo root?

---

## Workflow checklist

When the user asks to add a problem:

1. Confirm platform, topic (and subtopic only if clearly major), and count if missing.
2. Find a suitable problem (MCP search and/or problemset filters).
3. Fetch full statement from the official page.
4. Optionally read editorial; record editorial URL if found.
5. Write the markdown file using the template.
6. Do not modify unrelated files unless the user asks (e.g. README index).

---

## Example request the user might give

> Add 5 CSES DP problems (next after Dice Combinations).

Agent should: pick **5** problems in **that same topic**, create e.g. `CSES/dp/<ID>_<Title>.md` for each, full statement + link + intuition (root idea + mental picture) + walkthrough + pitfalls, C++ notation, no code, editorial link if found. Skip any already in the repo.
