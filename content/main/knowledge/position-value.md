---
title: Position Values
type: docs
weight: 3
math: false
---

# Position Values

Now that we understand game trees, we can start solving games! Our goal is to assign values to each position. These values tell us whether moving to that position leads to a win, loss, or tie if both players play perfectly. Don't worry if this sounds confusing right now. We'll break it down by solving a small game!

## 10-to-0-by-1-or-2

Imagine you are playing this game with a friend: there are 10 pennies in front of both of you. You can each remove either 1 or 2 pennies from the pile on your turn. The goal is to **not** be the player who reaches 0.

{{% hint info %}}
This is a simplified version of the classic game Nim. There is a strategy to always win and we will be deriving it!
{{% /hint %}}

Play the game on the GamesCrafters website below to get a feel for it! In this game, you are trying to force your opponent to reach 10 instead of 0.

<div style="display: flex; justify-content: center; margin: 20px 0;">
  <iframe
    src="https://nyc.cs.berkeley.edu/uni/games/0to10by1or2/variants/regular.com"
    style="width: 100%; max-width: 900px; height: 600px; border: 1px solid #ccc; border-radius: 8px;">
  </iframe>
</div>

## Constructing the Game Tree

Based on these rules, we can construct the game tree! For now, let us ignore win and lose conditions. The positions (vertices) are the different possible counts between 0 and 10, and the moves (edges) are subtracting either 1 or 2 from the current count.

Let's start with the starting position when the count is 10. From here, there are two moves we can make:

- You can take away 1, which will take us to a count of 9
- You can take away 2, which will take us to a count of 8

![Remove diagram](/images/knowledge/remove.png)

We can repeat this for every position in our game (count 0 to 10). Notice that when the game position has a count of 1, there is only one outgoing edge, since there is only one valid move (subtract 1) you can make from this position. In this game tree, our starting position 10 is our single source vertex and the primitive position 0 is our only leaf node. Every arrow either represents subtracting one or subtracting two.

![Full tree diagram](/images/knowledge/full_tree.png)

## Position & Move Values

Formally, the value of a position is "the outcome of the game for the player whose turn it is, assuming perfect play" (Cheung, 2023). Although we haven't yet formally defined the notion of perfect play, we can already deduce the value of primitive positions from this definition alone.

### Primitive Position Values

At a primitive position, you can no longer make any moves from the given state (no outgoing edges). In 10-to-0-by-1-or-2, reaching the state with a count of 0 means you have lost the game, so we assign this position the value LOSE.

Let's imagine we start with 0 items. Whoever plays first will immediately lose, since they have no valid moves. This means that whoever is at this position loses, so we assign it a LOSE value. In this case, the only primitive position is a losing one, but primitive positions can also be winning or tying.

We will be using the following to refer to and represent position values:

- Positions with a value of WIN will be referred to as winning positions, colored green in the game tree.
- Positions with a value of TIE will be referred to as tying positions, colored yellow in the game tree.
- Positions with a value of LOSE will be referred to as losing positions, colored red in the game tree.

![Primitive value diagram](/images/knowledge/primitive.png)

## How Values Are Assigned

To assign values to the other positions, we need to make some assumptions. We assume that both players play perfectly, meaning they always make the best possible move available to them. Since winning isn't always possible, we assume that players prefer outcomes in the following order: WIN > TIE > DRAW > LOSE. We also need to consider that as we move through the game tree, the turn alternates between players. We consider the perspective of the player whose turn it is.

### Losing Positions

{{% hint info %}}
The children of a position are the positions that can be reached by making one valid move from that position. In our diagram, these are the positions that the arrows point to.
{{% /hint %}}

A position is a losing position (LOSE) if **all** of its children are winning positions (WIN). Remember that turns alternate. When you make a move, you put your opponent in one of the child positions. If all the child positions of your current position are winning ones, your opponent can always win if they play perfectly. Therefore, you are in a losing position.

![Lose value diagram](/images/knowledge/lose.png)

### Winning Positions

A position is a winning position (WIN) if **at least one** of its children is a losing position (LOSE). If you can make a move that puts your opponent in a losing position, you can guarantee a win for yourself. You are playing perfectly, so if there is at least one path to victory, you will take it. Therefore, you are in a winning position.

![Win value diagram](/images/knowledge/win.png)

### Tying Positions

A position is a tying position (TIE) if **at least one** of its children is a tying position (TIE), and **none** of its children are losing positions (LOSE). If there is at least one losing position among the children, you would take that move and be in a winning position. You prefer tying over losing, so if there is at least one path to a tie and no paths to victory, you will take it. Therefore, you are in a tying position.

## Move Value

Just like positions, moves also have a value determined by the value of the position where the move leads:

- A winning move (WIN) is a move which gives the other player a losing position
- A losing move (LOSE) is a move which gives the other player a winning position
- A tying/draw move (TIE or DRAW) is a move which gives the other player a tying or drawing position

![Move value diagram](/images/knowledge/move-val.png)

We are now ready to solve our first game!

## Summary

- A position is a losing position (LOSE) if all of its children are winning positions (WIN)
- A position is a winning position (WIN) if at least one of its children is a losing position (LOSE)
- A position is a tying position (TIE) if at least one of its children is a tying position (TIE), and none of its children are losing positions (LOSE)
- Move values are determined by the value of the position where the move leads

## Practice Questions

<details>
<summary>Q1: Position A has two children, one WIN and one LOSE. What is the value of position A?

A) WIN<br>
B) LOSE<br>
C) TIE<br>
D) Cannot be determined
</summary>

**Answer: A** — Position A can move to a LOSE position for the opponent, so the current player takes that move and wins. A position is WIN if at least one child is LOSE.

</details>

<details>
<summary>Q2: Position B has three children, all of which are WIN positions. What is the value of position B?

A) WIN<br>
B) LOSE<br>
C) TIE<br>
D) DRAW
</summary>

**Answer: B** — Every move from B puts the opponent in a WIN position, meaning the opponent can always win. Since all children are WIN, the current player is stuck in a losing position.

</details>

<details>
<summary>Q3: A move is called a "winning move" if it leads to which type of position for the opponent?

A) A WIN position<br>
B) A LOSE position<br>
C) A TIE position<br>
D) A primitive position
</summary>

**Answer: B** — A winning move sends the opponent to a LOSE position. Move values are defined by what the resulting position means for the opponent, not for you.

</details>
