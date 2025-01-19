---
title: Homefun
type: docs
weight: 1
---

# **Homefun**

Due: `February 3, 2025`

{{< hint warning >}}

**Warning:** This page is under construction.

{{< /hint >}}

{{< tabs >}}

{{< tab "Logistics" >}}

This assignment is managed through a single Github classroom assignment. You can accept it via [this link](https://classroom.github.com/a/-W652rH9). This will create a new repository that you have write access to. Once you accept it, **read the `README` file.**

When you push commits to this repository, an automatic testing job will run pre-written tests. These tests verify that your program produces the right outputs on certain inputs. You can find verbal descriptions of each one in the "Checks" section. Further, all of the tests' expected input/output pairs are publically visible in the autograder workflow runs.

**If the latest commit on your Homefun repository's `main` branch passes all tests before the due date passes, you will receive full credit.** If you do not pass the necessary autograder tests before the due date passes, your work can be reviewed by an organizer on an effort basis upon request (so you should always let someone know if you won't finish in time).

{{< /tab >}}

{{< /tabs >}}

## Summary

Write a program that encodes a family of puzzles generically and uses that encoding to compute "perfect solutions" to concrete instances of them. Any programming language is acceptable. Optional aid is provided in the form of a Python template.

## Explanation

Here, we give a bare-minimum introduction to essential concepts you will encounter while you complete this assignment.

{{< hint warning >}}

We highly recommend you read through the appended [theoretic introduction](#appendix) for motivation and context, as well as the other [provided resources](#resources).

{{< /hint >}}

### Puzzles and Games

Puzzles are games in both a formal and informal sense; we play them to have fun, and in formal theory they are the single-player subclass of games. As such, we will introduce concepts that are applicable for games in general, even if you will only explore them in-depth for puzzles.

### Games and Rulesets

The games we are interested in are determined by what positions they can find themselves in, what players there are, how positions can transition to others, and by what positions are terminal (and the benefit associated with reaching them according to each player).

When we talk about games colloquially, we don't usually refer to all of these things. If someone refers to Chess, they are not referring to it in terms of its positions, but rather in terms of the **ruleset** that "generates" the game itself. In doing so, they can communicate a game with {{< katex inline >}} >10^{100} {{< /katex >}} unique positions in only about a page of rules.

The process of encoding a ruleset in a computer and obtaining a subset of positions in the game it generates (perhaps figuring out interesting facts about it in the process) is known as "exploration" or "search." This is what we will do in this Homefun.

### Our First Ruleset

The [wolf, goat, and cabbage problem](https://en.wikipedia.org/wiki/Wolf,_goat_and_cabbage_problem) is a very old logic puzzle, defined as follows:

* A farmer with a wolf, a goat, and a cabbage wants to cross a river by boat.
* The boat can carry only the farmer and a single item per trip across the river.
* If left alone together, the goat would eat the cabbage.
* If left alone together, the wolf would eat the goat.

It is quite a simple puzzle. Try figuring out a perfect strategy for it by hand or in your head. We will explore how you can have a computer do this. But before that, we will hint at how we might be able to represent this in code.

### The Puzzle Interface

All of the rulesets we are interested in have very clear conditions for success and failure, and they describe what the player (e.g., the farmer) can do. We can leverage this to write an interface for puzzles that we can later write generic code for.

```
interface Puzzle:
    function start() -> Position
    function get_moves(Position) -> Collection<Move>
    function do_move(Position, Move) -> Position
```

So now, if we wished to write code that could explore a puzzle for us, it could be defined generically with this interface. And later, the only work we would need to do is fill out the functions of this interface according to the next ruleset we are interested in.

### Families of Puzzles

The [missionaries and cannibals problem](https://en.wikipedia.org/wiki/Missionaries_and_cannibals_problem) is a very similar logic puzzle, where:

* A bishop with three missionaries and three cannibals wants to cross a river by boat.
* Only the bishop can row the boat, which can carry at most two additional people.
* The boat needs at least one more person apart from the bishop to float with stability.
* If cannibals are around less missionaries, they would eat them (even if the bishop is around).
* The bishop cannot stop cannibals, but the cannibals do not want to eat the bishop.

This puzzle is more complicated than the last, but you will notice that they have many things in common. In fact, they have so much in common that it might feel silly to re-implement the `Puzzle` interface for each one.

This situation happens a lot. Here, we call these puzzles **variants** of one another. In other words, they are under the same family of puzzles (in this case, [river crossing puzzles](https://en.wikipedia.org/wiki/River_crossing_puzzle)). Sometimes puzzles in the same family are so logically similar that it is better to implement the `Puzzle` interface once for the whole family, and then provide a way to instantiate concrete puzzles via a constructor.

{{< hint info >}}

Programmers decide convenient "levels of abstraction" for families of puzzles and games according to what they want. Implementing `Puzzle` for the family of river crossing puzzles may necessitate the use of hashmaps, so a programmer might avoid doing so because of how slow they are to use. Conversely, implementing `Puzzle` for each of the concrete rulesets might result in a lot of repeated code, which is also bad.

{{< /hint >}}

## Deliverable

Write an exploration module that only knows about the `Puzzle` interface, and from its provided methods, can generate strong solutions to concrete puzzles.

Then, encode both of the river crossing problems mentioned above as separate implementations of the `Puzzle` interface. As a challenge, make a single implementation of the `Puzzle` interface for a family of river crossing problems that includes both of these puzzles.

Finally, write code that allows your program to call the exploration module on an argument `Puzzle` implementation and print the generated strong solution to `STDOUT`.

{{< hint warning >}}

Regardless of your programming language and of whether you choose to create separate `Puzzle` implementations, your exploration module should not depend in any way on any information about either of the two rulesets (beyond the generic `Puzzle` interface).

{{< /hint >}}

## Checks

TODO

## Resources

Here are some things that might help you get started.

* [The appendix below](#appendix)
* [Dan's CalWeek 2020 Lecture](https://www.youtube.com/watch?v=YyTJOjgJD7w)
* [Dan's Computational Game Theory Slides](https://www.dropbox.com/scl/fi/gxp9xkxxehy8xaqk2fdlg/2019-08-28-Computational-Game-Theory.pdf?rlkey=6t07frdal6d05ysw10fg08i9x&e=1&dl=0)
* [Rogue AI Riddle](https://www.youtube.com/watch?v=qMFpOcLroOg&t=247s)

## Appendix

We provide an introduction to games by comparing them to optimization problems, and explain what solution concepts and strong solutions are. Finally, we present puzzles as 1-player games.

### Games vs. Problems

A single-objective optimization problem attempts to maximize or minimize a value with respect to a set of parameters. For example, the optimization problem

{{< katex display >}}

\max_{0 \leq x \leq 1} f(x)

{{< /katex >}}

asks a very clear question: "Given {{< katex inline >}} f : \mathbb{R} \to \mathbb{R} {{< /katex >}} and {{< katex inline >}} x \in [0, 1]{{< /katex >}}, what is the maximum possible value {{< katex inline >}} f {{< /katex >}} can take?" However, a game is a _situation_, not a problem. We will skip a proper definition, but the cornestone assumption is that each player attempts to maximize their own [utility](https://en.wikipedia.org/wiki/Utility). But from a global perspective, there is no obvious "ask" when presented with a game (unlike optimization problems).

It is tempting to believe that there exists some "universally optimal" way of playing any game, and therefore a way of predicting how any game will be played. It turns out this is not true under most conditions. This is non-obvious, and took a lot of bright minds to figure out. However, for many specific kinds of games, there exist very reasonable rules that can be used to predict how a game will be played _a priori_.

### Solution Concepts

Each different way to formulate such rules is called a [solution concept](https://en.wikipedia.org/wiki/Solution_concept). A very famous solution concept is the [Nash Equilibrium](https://en.wikipedia.org/wiki/Nash_equilibrium). It tells us to look at all of the possible strategies that players could implement, and look for groups of strategies (one per player) where no player could be better off by only changing their own strategy. This applies to games where players cannot cooperate. Each of these strategy "profiles" (a one-per-player set of strategies) is called a Nash Equilibrium.

In a very specific subset of games that are deterministic, [complete-information](https://en.wikipedia.org/wiki/Complete_information), [sequential](https://en.wikipedia.org/wiki/Sequential_game), finite, and either [non-cooperative](https://en.wikipedia.org/wiki/Non-cooperative_game_theory) or [zero-sum](https://en.wikipedia.org/wiki/Zero-sum_game) (such as Chess), there is an efficient way of computing such strategy profiles known as [backward induction](https://en.wikipedia.org/wiki/Backward_induction), which refers to the procedure of analyzing players' choices "in reverse," assuming everyone does what is best for them. In the specific subcase of zero-sum games, this is implemented by the popular [Minmax algorithm](https://en.wikipedia.org/wiki/Minimax).

### Strong Solutions

The game-theoretic value of a position is the highest utility that a player whose turn it is at that position can guarantee to obtain for themselves through their own actions. Oftentimes, the way in which we compute optimal strategies under different solution concepts is by storing a mapping of all positions in a game to their values. When we have such a mapping, we also have access to the best possible strategies in the corresponding game from any position.

Such a mapping is equivalent to something known as a [strong solution](https://en.wikipedia.org/wiki/Solved_game), as it provides the answer to the question: "Given any position in the game, what is the best possible course of action?" It is not obvious why this is possible; to figure out the best possible action from a position, it suffices to look at the values of all possible positions that the player who is "in power" at that position could transform the game into, and materialize the one that is most utilitarian for that player.

{{< hint info >}}

While the name may suggest so, a strong solution is not a solution concept. A solution concept is a convention of sorts, while a strong solution is actual data.

{{< /hint >}}

### Games to Puzzles

Removing the complexity of conflicting player interests makes single-player games more akin to single-objective optimization problems, as opposed to the complicated situations that their multi-player cousins are likened to.

This fact is constantly reflected in the systems you will be building on top of; sometimes they will be generic, using the same infrastructure for multi-player games and puzzles; other times, there will be dedicated platforms for puzzles, which allow builders to cut the corners that are only necessary when dealing with multi-player games.

Regardless, all of the vocabulary that is used when talking about multi-player games (e.g., strong solutions) is still valid for puzzles because they are still games. In this assignment, we opt to be as generic as possible so that everything you learn is applicable when you think about general games.
