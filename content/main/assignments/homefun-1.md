---
title: Homefun I 
type: docs
weight: 1
---

# **Homefun I**

Due date: `9/9/2024`

## Summary

Write a program that computes the strong solution of two related games, `10-to-0-by-1-or-2` and `25-to-0-by-1-3-or-4`. Any programming language is acceptable. Optional aid is provided in the form of a Python template.

## Logistics

All Homefuns are managed through a single Github classroom assignment. You can accept it [here](.). This will create a new repository that you have write access to. **Read the `README`.** When you push new commits on this repository, an "autograding" job will run the tests for all Homefuns.

These tests simply verify that your program has the correct outputs for certain inputs. Each Homefun has a set of input/output pairs that the tests enforce. **If the latest commit on your Homefun repository's `main` branch passes all of a given Homefun's tests before its due date passes, you will receive full credit for the Homefun.**

This means that you may not need to pass all "autograding" tests to complete a particular Homefun (because they include other Homefuns' tests), but you should pass all tests once you complete all Homefuns. **The tests belonging to each Homefun are made public on their pages.** If you do not pass the necessary autograder tests before a due date passes, your work will be reviewed by an organizer on an effort basis.

{{< hint warning >}}

If you experience exceptional circumstances and require accommodations, reach out to a course organizer as soon as you can to discuss how we can help you.

{{< /hint >}}

## Introduction to Games 

### Games vs. Problems 

An optimization problem attempts to maximize or minimize an objective with respect to a set of parameters. For example, the optimization problem

{{< katex display >}}

\max_{0 \leq x \leq 1} f(x)

{{< /katex >}}

asks a very clear question: "Given {{< katex inline >}} f : \mathbb{R} \to \mathbb{R} {{< /katex >}} and {{< katex inline >}} x \in [0, 1]{{< /katex >}}, what is the maximum possible value {{< katex inline >}} f {{< /katex >}} can take?" However, a game is a _situation_, not a problem. While we will visit proper definitions later, the cornestone assumption is that each player attempts to maximize their own utility in these situations (whatever that may mean). But from a global perspective, there is no obvious "ask" when presented with a game (unlike optimization problems).

It is tempting to believe that there exists some "universally optimal" way of playing any game, and therefore a way of predicting how any game will be played. It turns out this is not true under most conditions. This is non-obvious, and took a lot of bright minds to figure out. However, for many specific kinds of games, there exist very reasonable rules that can be used to predict how a game will be played _a priori_.

### Solution Concepts

Each different way to formulate such rules is called a [solution concept](https://en.wikipedia.org/wiki/Solution_concept). A very famous solution concept is the [Nash Equilibrium](https://en.wikipedia.org/wiki/Nash_equilibrium). It tells us to look at all of the possible strategies that players could implement, and look for groups of strategies (one per player) where no player could be better off by only changing their own strategy. This applies to games where players cannot cooperate. Each of these strategy "profiles" (a one-per-player set of strategies) is called a Nash Equilibrium.

In a very specific subset of games that are deterministic, [complete-information](https://en.wikipedia.org/wiki/Complete_information), [sequential](https://en.wikipedia.org/wiki/Sequential_game), finite, and either [non-cooperative](https://en.wikipedia.org/wiki/Non-cooperative_game_theory) or [zero-sum](https://en.wikipedia.org/wiki/Zero-sum_game) (such as Chess), there is an efficient way of computing such strategy profiles known as [backward induction](https://en.wikipedia.org/wiki/Backward_induction), which refers to the procedure of analyzing players' choices "in reverse," assuming everyone does what is best for them. In the specific subcase of zero-sum games, this is implemented by the popular [Minmax algorithm](https://en.wikipedia.org/wiki/Minimax). For the remainder of the Homefun series, we will only talk about this kind of game. 

### Strong Solutions 

The game-theoretic value of a position is the highest utility that a player whose turn it is at that position can guarantee to obtain for themselves through their own actions. Oftentimes, the way in which we compute optimal strategies under different solution concepts is by storing a mapping of all positions in a game to their values. When we have such a mapping, we also have access to the best possible strategies in the corresponding game from any position. 

Such a mapping is equivalent to something known as a [strong solution](https://en.wikipedia.org/wiki/Solved_game), as it provides the answer to the question: "Given any position in the game, what is the best possible course of action?" It is not obvious why this is possible; to figure out the best possible action from a position, it suffices to look at the values of all possible positions that the player who is "in power" at that position could transform the game into, and materialize the one that is most utilitarian for that player.

{{< hint info >}}

Remember that this is only referring to the subset of games described above. For example, we can only talk about a player "in power" because we assume the game is sequential.

{{< /hint >}}

{{< hint info >}}

While the name may suggest so, a strong solution is not a solution concept. A solution concept is a convention of sorts, while a strong solution is actual data.

{{< /hint >}}

## Game Representation 

### Games and Rulesets 

Having narrowed down the games we will be dealing with, we can provide a semi-formal definition of a game. Games in this subset are determined by what positions they can find themselves in, what players there are, how positions can transition to another, and by what positions are terminal (and what benefit is associated with reaching them). 

When we talk about games colloquially, we don't usually refer to all of these things. If someone refers to Chess, they are not referring to it in terms of its positions, but rather in terms of the **ruleset** that "generates" the game itself. In doing so, they can communicate a game with {{< katex inline >}} >10^{100} {{< /katex >}} unique positions in only about a page of rules, which can be memorized.

The process of encoding a ruleset in a computer and obtaining a subset of positions in the game it generates (perhaps figuring out interesting facts about it in the process) is known as "exploration" or "search." This is what we will do in this Homefun, for two specific rulesets.

### Our First Ruleset 

The game `10-to-0-by-1-2` is generated by the following ruleset: 

* There is a collection of 10 items.
* 2 players take alternating turns removing either 1 or 2 items from the collection.
* Player 1 starts. The player who takes the last item from the collection wins.

It is quite a simple game. Try playing it with someone else to become familiar with it. You can even try figuring out a perfect strategy for it by hand or in your head, but it is somewhat difficult. We will explore how you can have a computer do this. But before that, we will hint at how we might be able to take advantage of modern generic programming by introducing the idea of a game family.

### Our Second Family 

The `zero-by` family is the set of games achievable via the parameterization `N-to-0-by-S`, where `N` is a positive integer and `S` is a set of positive integers. The generic ruleset is as follows:

* There is a collection of `N` items.
* 2 players take alternating turns removing an amount of items in the set `S` from the collection.
* Player 1 starts. The player who takes the last item from the collection wins.

It is also possible to parameterize this ruleset on player count; we decide how abstract we make a family's parameterization. We motivate this decision by semantics and the available provisions for generic constructs in our programming language of choice.

Ultimately, a game family is the set of games describable by concretizing the parameters of its generic ruleset. The games within a game family are often referred to as "variants" of each other, and games which belong to obvious families are sometimes called "variable."

### The Game Interface

We will work with the following way of programmatically characterizing any ruleset: 

```none
interface Game:
    function start() -> Position 
    function get_moves(Position) -> Collection<Move>
    function do_move(Position, Move) -> Position
    function terminal_value(Position) -> Value
```


{{< hint info >}}

This is not a real programming language, but the idea of an interface is central to generic programming in most programming languages.

{{< /hint >}}

The `start` function provides a way to specify how a game starts. The `get_moves` function returns a collection of things you can do from a given position; `do_move` applies one of those things onto a position and returns the resulting position, assuming it is allowed. In the case of terminal positions, `terminal_value` specifies the benefit obtained by the player whose turn it is at that position.

This interface is very general, but it does assume a turn order (e.g., alternating play). Including a `turn(Position) -> Player` function fixes this. There are many other (perhaps more elegant) ways to present the `Game` interface, but we have found that this is an intuitive first impression.

## Deliverable

Implement the rulesets `10-to-0-by-1-or-2` and `25-to-0-by-1-3-or-4`, write a single solving algorithm that computes the mappings of positions in each of these games to their values, and print the mappings in a specific format (shown in the next section). 

Remember that these two games are part of a very intuitive family; you should not need to repeat much logic, if any at all. Here are some steps we recommended you take to do this:

1. Read through the Python template, making sure to read the `README`.
2. Make a mental path of how inputs enter the program, and of how outputs are created.
3. Take note of the `TODO` hints left throughout the template.
4. Design tests that the functions you implement will need to pass.
5. Implement your solution, and iterate using your tests.

## Resources

Here are some things that might help you get started:

* [Dan's CalWeek 2020 Lecture](https://www.youtube.com/watch?v=YyTJOjgJD7w)
* [Dan's Computational Game Theory Slides](https://www.dropbox.com/scl/fi/gxp9xkxxehy8xaqk2fdlg/2019-08-28-Computational-Game-Theory.pdf?rlkey=6t07frdal6d05ysw10fg08i9x&e=1&dl=0)

## Checks 

The following commands will be ran from your submission's top-level directory verbatim. We will expect to see the output shown below each of them in `STDOUT`. There will be no input provided through `STDIN`. If you are using the template, you should just need to write `print` statements.

### `10-to-0-by-1-or-2`

Command:
```
make run ARGS="10 1 2"
```

Output:
```
OUTPUT
```

### `25-to-0-by-1-3-or-4`

Command:
```
make run ARGS="25 1 3 4"
```

Output:
```
OUTPUT
```
