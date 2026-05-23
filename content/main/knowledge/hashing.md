---
title: Hashing Positions
type: docs
weight: 5
math: true
---

# Hashing Positions

Solving a game means exploring an enormous game tree. This can be computationally expensive to compute every time we want to play the game, especially for bigger games. To avoid recomputing positions over and over, we need a way to **name** each position with a number so we can store and look it up quickly. That "naming function" is our **hash function**.

## Big Idea: Memoization

Our solvers use **memoization** to be more efficient. This means that, once a position has been evaluated, we store its value instead of recomputing it. This is just like caching Fibonacci values instead of using recursion to find every value. We can illustrate this with a simple Fibonacci function. We can access previously computed values by storing pairs `(n, fib(n))` in a table.

```c
int fib(int n) {
    if (n == 0 || n == 1)
      return n;
    else if we've already computed fib(n):
      return stored value;
    else
      int value = fib(n - 1) + fib(n - 2);
      store(n, value);
      return value;
}
```

![Fibonacci recursion tree](/images/knowledge/fib.png)

For games, we can't use a simple number to store a position's value. We need to represent the entire board state, including whose turn it is, where the pieces are, and so on. How do we map this data into an integer that will serve as an index into our table? This mapping function is called a **hash function**.

## What Is a Hash Function?

A **hash function** takes a piece of data (like a board) and returns an integer. We use that integer as an index into an array or as a key in a table.

For our solvers, a good hash function should:

- Map each reachable position to a unique integer
- Spread different positions out evenly across the table
- Be fast enough to compute on every recursive call

## Writing a Tic-Tac-Toe Hash Function

Let's consider how we might write a hash function for Tic-Tac-Toe. Here's how you play:

- One player chooses X, the other chooses O
- Two players take turns placing Xs and Os on a 3×3 grid
- Assume X goes first
- Once a piece is placed, it isn't moved
- The first player to get three in a row wins
- If the board fills up with no winner, it's a tie

![Tic-Tac-Toe board](/images/knowledge/Tic-Tac.png)

How do we convert a Tic-Tac-Toe position into a unique integer? One idea is to turn the 2D board into a 1D array of 9 slots:

![Flattened board](/images/knowledge/flat.png)

Each slot can be in one of three states: blank, O, or X. We can represent these as three numbers instead:

- `0` for blank
- `1` for O
- `2` for X

![Board encoded as digits](/images/knowledge/hashed.png)

If we treat this as a base-3 number (a "ternary" number), we could hash a position by interpreting the 9-digit ternary number as an integer. That gives us up to `3^9 = 19683` possible codes.

## Optimizing the Hash Function

If we understand the rules of the game, we can do better than just using all `3^9` possible combinations. For Tic-Tac-Toe:

- Players alternate turns
- X always goes first
- You can never have more than one extra X than O on the board

This means many of the boards our hash function can encode are impossible in real play, like boards with 5 Os and 1 X.

We can count the number of **legal** boards using combinatorics. Let `s` be the number of slots.

Small examples:

- `s = 1`: 2 boards (`-`, `X`)
- `s = 2`: 5 boards (`--`, `-X`, `X-`, `XO`, `OX`)
- `s = 3`: 13 boards

This pattern continues based on how many Xs and Os are allowed at each depth.

### Using Combinations

For example, the number of ways to choose `k` locations from `n` is:

n choose k = n! / (k! (n - k)!)

This lets us count valid arrangements of Xs and Os.

### Blur Method

1. Choose locations for all `x + o` pieces: `C(s, x + o)`
2. Choose which of those positions are Xs: `C(x + o, x)`
3. Multiply both together.

### Overcount Method

1. Count all permutations: `s!`
2. Divide by repeated symbols: `x! o! (s - x - o)!`

Both methods give the same result.

Using this method for full Tic-Tac-Toe, there are only **6046 legal boards**, not 19683.

## Finding a New Hash Function

We can use **bias + rearrangement** to produce a **combinatorially optimal hash**:

1. **Bias (offset)**
   Count how many legal boards exist in all layers before the current one.

2. **Rearrangement**
   Rank positions *within* the current layer using combinatorial counts.

## Example: Hashing a Small Board

Suppose we hash the toy board: `XO-X` (4 slots).

![Stair-step buckets](/images/knowledge/stairs.png)

1. **Bias:**
   If `X = 2`, `O = 1`, `S = 4`, count how many boards come before this layer.
   Example: `1 + 4 + 12 = 17`

2. **Rearrangement:**
   Count how many legal rearrangements come before `XO-X` inside its layer.
   Example: `3 + 3 + 2 = 8`

Final hash:

hash = bias + rearrangement = 17 + 8 = 25

## Practical Considerations

Combinatorially optimal hash functions are powerful, but they can be expensive for larger games. In practice, we often use simpler hashes that are:

- Easy to compute
- Well-distributed
- Low-collision

We can also use **symmetry** (rotations and reflections) to reduce the total number of stored positions.

## Summary

- **Memoization** saves time by remembering solved positions
- **Hash functions** map complex positions to integers
- **Combinatorial hashing** packs only legal boards into small ranges
- **Bias + rearrangement** builds optimal hashes
- In practice, we balance optimality with efficiency

## Practice Questions

<details>
<summary>Q1: What is the purpose of memoization in a game solver?

A) To generate all possible moves from a position
B) To store already-computed position values so they don't need to be recomputed
C) To compress the board state into a smaller representation
D) To ensure two different boards never get the same hash value
</summary>

**Answer: B** — Once a position's value has been computed, it gets stored in a table indexed by its hash. Any future visit to that position returns the stored value immediately instead of re-exploring the subtree below it.

</details>

<details>
<summary>Q2: A naive hash for Tic-Tac-Toe assigns each of the 9 slots one of 3 values. How many total board states does this encode?

A) 9 × 3 = 27
B) 9! = 362,880
C) 3^9 = 19,683
D) 6,046
</summary>

**Answer: C** — Each of the 9 slots independently takes one of 3 values (blank, O, X), giving 3^9 = 19,683 possible encodings. This includes many illegal boards. The combinatorial hash reduces this to just the 6,046 actually reachable positions.

</details>

<details>
<summary>Q3: Why does a Tic-Tac-Toe board with 5 Os and 1 X not need a hash entry?

A) Because it has fewer than 9 pieces on the board
B) Because O always goes second and can never win
C) Because the hash function cannot encode more than 4 pieces
D) Because it is unreachable
</summary>

**Answer: D** — Players alternate turns and X always goes first, so the number of Xs on the board is always equal to or one greater than the number of Os. A board with 5 Os and 1 X violates this constraint and can never be reached through legal play.

</details>

<details>
<summary>Q4: In the bias + rearrangement hashing scheme, what does the bias (offset) represent?

A) The total number of legal boards with fewer pieces than the current position
B) The number of Xs minus the number of Os on the board
C) The index of the first blank slot on the board
D) The number of winning positions reachable from the current state
</summary>

**Answer: A** — The bias is an offset that skips over all the hash entries already used by boards with fewer total pieces. It counts all the legal boards in every earlier bucket in the zigzag pattern, so that boards with different piece counts never collide in the hash table.

</details>

<details>
<summary>Q5: The combinatorial hash reduces Tic-Tac-Toe from 19,683 entries to 6,046. What is the main reason this reduction is possible?

A) Many board states are symmetric and can be merged into one entry
B) The solver only visits positions reachable from the starting state
C) Game rules constrain which piece counts are legal, eliminating thousands of impossible boards
D) Tic-Tac-Toe ends before all 9 slots are filled in most games
</summary>

**Answer: C** — The reduction comes from applying the alternating-turns rule: X always goes first, so the count of Xs is always equal to or one more than the count of Os. This eliminates all boards that violate this constraint, shrinking the table from 19,683 naive encodings down to the 6,046 boards that can actually arise in a real game.

</details>
