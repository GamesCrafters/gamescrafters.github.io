---
title: Game Trees
type: docs
weight: 2
math: false
---

# Game Trees

To solve a game, we need to be able to know every possible position and move. This allows us to find winning and losing strategies by exploring all possible outcomes. In this chapter, we'll introduce the concept of **game trees**, which provide a structured way to visualize and analyze all possible game states and moves.

## What Is a Game Tree?

{{% hint info %}}
If you are new to the concept of trees, consider checking out **CS61A** and **CS61B**'s materials on the topic! We will briefly cover the necessary concepts here, but a deeper understanding will be very helpful. We recommend watching [Introduction to Trees and Traversals](https://youtu.be/wkkCVWn7au4?si=D7nl7VQ-jAncMpP6) before continuing.
{{% /hint %}}

A game tree helps us visualize our game's **state space**, the set of all possible states and actions. A **tree** is a connected graph (a collection of points and lines where there is a path between every pair of vertices) with no cycles (loops). In a game tree, the **nodes** represent the different positions of the game and the **edges** represent the moves between the different positions. Below is an example of a game tree for Tic-Tac-Toe:

![Game tree diagram](/images/knowledge/ttt_game_tree.svg)

## Positions

The various ways we can arrange the pieces on our board are our game states or **positions**. In the tree, these positions are represented by vertices or the **nodes** that form the graph. The **start position** is the starting arrangement of our board and is the single node at the top of the tree, represented by a **single source vertex**. By definition, we can trace any vertex or position in our tree back to the single source vertex! When we finish playing a game, we can't make any more moves. These terminal or **primitive positions** live at the bottom of our tree and are represented by **leaf nodes**, nodes with no outgoing arrows.

![Position diagram](/images/knowledge/position.svg)

## Moves

The **moves** a player makes to go from position A to position B are represented by an **edge** connecting vertex A to vertex B. Even though edges can represent movement in both directions, the edges for games are one-way arrows to represent the forward motion of time as we go from one game position to the next. This is why game trees are **directed** graphs, or graphs with edges that point in a specific direction.

{{% hint info %}}
Check out the chapter Tree Traversals and Graphs in **CS61B** for a deeper dive into the difference between undirected and directed graphs!
{{% /hint %}}

![Move diagram](/images/knowledge/move.svg)

Putting this all together, we get our game tree! We are able to represent the state space as a directed Graph G = (P, M), where P is the set of positions (vertices) and M is the set of moves (edges) in a game.

## Summary

- The nodes are all the possible positions of the game.
- The single source vertex is the first node in our tree and the game's initial position.
- The leaf nodes are the terminal states or the states from which we can no longer make a move (e.g. win, lose, tie).
- The edges are all the possible moves from one game state to the next. We make a move in the direction of the arrow.

## Solving a Game

Now that we have a way to represent all the possible positions and moves in our game, we can use this structure to analyze and solve the game. In the next chapter, we will explore how to assign values to each position in the game tree, which will help us determine the best strategies for winning the game.

## Practice Questions

<details>
<summary>Q1: In a game tree, what does a leaf node represent?

- A) A position where the current player has the most moves
- B) A terminal position where no further moves can be made
- C) The starting position of the game
- D) A position that only one player can reach
</summary>

**Answer: B** — Leaf nodes are terminal positions where the game ends and no outgoing edges exist. They sit at the bottom of the tree.

</details>

<details>
<summary>Q2: Why are the edges in a game tree directed (one-way arrows) rather than undirected?

- A) To show which player made the move
- B) Because the game board can only be set up in one orientation
- C) To represent the forward motion of time; each move leads to a new game state
- D) To ensure the tree has no cycles
</summary>

**Answer: C** — Directed edges capture the irreversibility of moves over time. You go from position A to position B by making a move; you don't travel backwards in a game.

</details>

<details>
<summary>Q3: In the game tree for Tic-Tac-Toe, what does the source vertex (root node) represent?

- A) X winning with three in a row
- B) The empty board before any move is made
- C) The position after X plays in the center
- D) The position with the most possible next moves
</summary>

**Answer: B** — The source vertex is the single starting position, the empty 3x3 board. Every other node in the tree is reachable from this root by following directed edges.

</details>
