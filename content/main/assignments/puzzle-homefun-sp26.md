---
title: HomeFun 1
type: docs
weight: 1
math: true
---

# **HomeFun 1**

Due: `10:59 AM 2026-02-02`

## Summary

You will implement a **loop-free** and a **loopy** puzzle as directed graphs, then compute and display the remoteness of each node (state) relative to the goal state `0`.

A puzzle is loopy if its graph representation contains a cycle, and loop-free otherwise. 

### Remoteness Definition

 For a node `v`, `remoteness[v]` is the **minimum number of legal moves needed to reach node `0`**.
 If node `v` cannot reach `0`, its remoteness is ∞ (infinity).

---

## Puzzle Rules (Graph Construction)
### Nodes
All integers from 0 to N inclusive are nodes in the graph.

### Legal moves (Directed Edges)

From a node `x`, you may move to:

- `x - 1` if `x` - 1 >= 0
- `x - 2` if `x` - 2 >= 0

#### Special Rule (for loop-free only): multiples of 3 are “dead ends.”
If node `x` is a multiple of 3, then:
- `x` has no outgoing edges (no legal moves from that node),
- Except node `0` is still a valid node (it’s the goal), but it also has no outgoing moves anyway.


So, for example, with `N = 10`:
- 9, 6, 3 have no outgoing neighbors
- 10 → {9,8} would normally exist because the “dead end” rule applies only to the current node; 10 is not a dead end, so those edges exist.
- However, 9 is a dead end, so from 9, you cannot move anywhere.

<div class="side-by-side">
  {{< figure src="/images/assignments/loopy_puzzle_graph.jpeg"
             caption="Loopy Puzzle Graph" >}}

  {{< figure src="/images/assignments/non_loopy_puzzle_graph.png"
             caption="Non-Loopy Puzzle Graph" >}}
</div>

---

## What You Must Produce

### 1) Graph Construction
Write a function that constructs the **directed adjacency structure** for nodes `0..N`, following the rules described above.

Your implementation **must work for arbitrary `N`**.  
**Do not hard-code** the graph for `N = 10`.

### 2) Remoteness Computation
Write a function that computes the **remoteness value for every node**.

#### Important Notes
The graph should be constructed and traversed in such a way that the correct remoteness is displayed for any size N (DO NOT hard-code the graph for N = 10).

Edges point toward smaller numbers (toward `0`), so a clean way to compute all remoteness values is:
- Run a shortest-path traversal starting **from the goal** (`0`), but on the **reversed graph** (i.e., follow incoming edges).
- This gives you the minimum number of moves to reach `0` from every node.

(You can implement this by building a reverse adjacency list, or by scanning edges and constructing incoming lists.)

---

## Output: Text User Interface (TUI)

Print each node’s remoteness in the format:
`node`: `remoteness`
For unreachable nodes, print inf or any other representation of infinity.

Use descending order from N down to 0 (to match the example shown in the lecture).

|Example Output (for N = 10, Loop-Free) | Output Example (for N = 10, Loopy) |
|----------|----------|
| 10:6<br>9:inf<br>8:5<br>7:4<br>6:inf<br>5:3<br>4:2<br>3:inf<br>2:1<br>1:1<br>0:0 | 10:6<br>9:7<br>8:5<br>7:4<br>6:5<br>5:3<br>4:2<br>3:3<br>2:1<br>1:1<br>0:0 |

---

## Submission Details

### Due **Monday, February 2nd, before the GamesCrafter’s Meeting (10:59 AM)**.
### Submission Form: [HomeFun 1 Submissions Google Form](https://forms.gle/JVWYRbSFdV7DeNoAA)

At a minimum, submissions should include:
- Source code implementing the puzzle rules and remoteness computation
- The required TUI output for BOTH versions for N = 10

## Academic Integrity
All submitted work must be your own. You may discuss high-level ideas with classmates, but you may not share or copy code. For this assignment alone, use of AI is strictly prohibited!

<style>
.side-by-side {
  display: flex;
  gap: 1.5rem;
  justify-content: center;
}

.side-by-side figure {
  width: 45%;
  text-align: center;
}
</style>
