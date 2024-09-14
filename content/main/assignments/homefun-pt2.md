---
title: Homefun Pt. 2
type: docs
weight: 1
---

# **Homefun Part 2**

Due: `11:59PM 9/18/2024`

{{< hint warning >}}

**Warning:** This page is under construction.

{{< /hint >}}

{{< tabs >}}

{{< tab "Logistics" >}}

Both parts of the Homefun are managed through a single Github classroom assignment. You can accept it [here](https://classroom.github.com/a/-W652rH9). This will create a new repository that you have write access to. **Read the `README`.** When you push new commits on this repository, an "autograding" job will run the tests for both parts.

These tests simply verify that your program has the correct outputs for certain inputs. Each part has a set of input/output pairs that the tests enforce. **If the latest commit on your Homefun repository's `main` branch passes all of a given part's tests before its due date passes, you will receive full credit for that part of the Homefun.** You can see which tests belong to which part in the assignments' "Checks" sections.

This means that you may not need to pass all "autograding" tests to complete a particular part (because they include the other part's tests), but you should pass all tests once you complete both. **All of the tests' expected input/output pairs are publically visible in the autograder workflow runs.** If you do not pass the necessary autograder tests before a due date passes, your work will be reviewed by an organizer on an effort basis.

{{< /tab >}}

{{< /tabs >}}

## Summary

Bolster the solver you wrote for Part 1 to handle `100-to-0-by-1-or-2`. Implement the ruleset of Tic-Tac-Toe, and write a hash function and its inverse to allow the solving algorithm you wrote to interface with this new `Game` implementation. 

## Game Abstraction 

In Part 1, we provided a semi-formal definition of a game in terms of the positions it could find itself in. This heavily relied on intuition and put a lot of weight on the term "position."

It turns out that the idea of a position is a conceptual add-on to games. That is, games do not "come with" positions; they are expressed entirely in terms of sequences of actions taken by players. Such a sequence of actions is called a "history."

In this section, we will bridge the ideas of a "history" and a "position." We will then observe that our existing understanding of a "position" is arbitrary. Finally, we will relax this preconceived notion into a class of techniques known as "game abstraction" that are useful when exploring "bigger" games.

{{< hint warning >}}

Note that we are still talking about a restricted subset of games, as explained in Part 1.

{{< /hint >}}


### Naive Solvers

In Part 1, you may have written a solving algorithm that looks somewhat like the following pseudocode in order to solve the `zero_by` family of games:

```
function solve(position)
    if position is terminal
        return value of position
    else
        opponent = worst_value(
            solve(result of taking action from position)
            for action in possible actions from position
        ) 
        return opposite_value(opponent)
```

This essentially says that the value of a position is its terminal value (if it has one), or the opposite of the worst possible outcome that can be forced into the opponent's hands (e.g., if we can force the opponent to lose then we can win). If we take a look at the [call graph](https://en.wikipedia.org/wiki/Call_graph) of this recursive function when applied to Tic-Tac-Toe, we may see something like this:

![](/ttt_call_graph.png)

From this, we can observe that the `solve` function is called once for each possible sequence of actions in the game. This tells us that by writing our solver this way we have effectively encoded the game by its set of histories {{< katex inline >}} H {{< /katex >}}, and that the amount of time our solver will take to terminate will be proportional to {{< katex inline >}} |H| {{< /katex >}}.

### Position Semantics 

Without preamble, a position {{< katex inline >}} p \in P {{< /katex >}} is a symbol that represents its own preimage under some function {{< katex inline >}} f : H \to P {{< /katex >}} (a function that takes a history and returns a symbol from a set {{< katex inline >}} P {{< /katex >}}). 

We will unpack this confusing definition by talking about a function {{< katex inline >}} f {{< /katex >}} that makes it intuitive. In casual conversation, we usually take {{< katex inline >}} f(h) {{< /katex >}} to be "the board resulting from the application of all of the actions in {{< katex inline >}} h {{< /katex >}} to the starting game board," and {{< katex inline >}} P {{< /katex >}} to be the set of all possible physical board configurations. For example, using this interpretation of {{< katex inline >}} f {{< /katex >}} and {{< katex inline >}} P {{< /katex >}}, we would come to the conclusion that we are calling `solve` twice on the same position in the previous example:

![](/ttt_position_mapping.png)

{{< hint warning >}}

**Why don't we just say that positions are board states?**

Not all games have boards, and not all boards carry all of the information we care about in a game. For example, a board of Chess does not indicate whether the [50-move rule](https://www.chess.com/terms/50-move-rule-chess) is active.

{{< /hint >}}

### Abstract Strategy Games 

We did not go to the trouble of defining positions this way just out of fear of picking wrong game representations. This is kind of mistake is not silent -- when the time comes to use your symbols, you simply would not have the information you need. 

The real reason we choose these mathematics is to "game" abstract strategy games. By involving these constructs, we can take advantage of the fact that their rulesets are expressed in terms of symbols to begin with (which is what gives them the "abstract" qualifier). Because they are expressed in terms of abstract symbols (such as game boards or mathematical objects like sets), their histories oftentimes exhibit the structure that the symbols themselves inherently carry.

This means quite a few things. To begin with, we can almost exclusively transact in symbols when analyzing this class of games. This is nice, because they are a lot easier to encode than histories (see the [position hashing](#position-hashing) section). However, we can also take advantage of much more subtle aspects of these symbols, and use the freedom we have when definining them to our benefit.

### Symbolic Reduction 

One of the clearest mappings we could establish from histories to some symbolic representation is to simply write down strings of actions. For example, a position of Tic-Tac-Toe could be:

```
[top left X][middle middle O][middle left X]
```

However, this is equivalent to the position:

```
[middle left X][middle middle O][top left X]
```

Here, we can apply our first trick; we will choose symbols that reflect the fact that these two histories are the same. This is easy, since the board already accomplishes this:

```
X |   |  
- + - + -
X | O | 
- + - + -
  |   |
```

In making this change, we have already cut down the number of symbols we will ever see by a significant amount. But observe the following position, which is symmetric to the one above:

```
  |   |  
- + - + -
X | O | 
- + - + -
X |   |
```

We can define a new function {{< katex inline >}} g : P \to P' {{< /katex >}} that takes a symbol from the set of possible board configurations and returns one of its "symmetries," in such a way that 

{{< katex display >}} g(p) = g(q) \iff p \text{ is symmetric to } q. {{< /katex >}} 

With the understanding that {{< katex inline >}} f : H \to P {{< /katex >}} maps game histories to game boards, we can compose {{< katex inline >}} g \circ f {{< /katex >}} to obtain a new function that both preserves the game's structure and reduces the amount of symbols (and therefore positions) necessary to analyze it even further.

{{< hint info >}}

It is okay if you do not understand this technique. It takes time to internalize, and you will not need it for this part. Interpret it as "treat all symmetries of the same board as one position."

{{< /hint >}}

Techniques like the ones above, which reduce the amount of positions in a game in a way that retains information about its history structure through a wise choice of symbolic mappings, collectively form what is known as "game abstraction."

### Positional Solvers

To reap the benefits of a reduced position count, we must ensure that we only explore the reduced symbol space. The most general approach to doing this is to interpret the structure of this space as a graph, where symbols are nodes connected by actions. This is because some games that exhibit possibly infinite play can be reduced to finite-position graphs that include cycles.

In cases where infinite play is impossible (regardless of strategy), the "game graph" (which is to symbols what "game tree" is to histories) is [a DAG](https://en.wikipedia.org/wiki/Directed_acyclic_graph). In such cases, a naive solving algorithm such as the one shown in the [naive solvers](#naive-solvers) subsection can be adapted to only explore the space of symbols via [memoization](https://en.wikipedia.org/wiki/Memoization). An example of such a game is Tic-Tac-Toe. This would have a form similar to this:

```
function solve(position, seen)
    if position has been seen 
        return value of position

    if position is terminal
        mark position as seen 
        store position's value
        return value of position

    else
        opponent = worst_value(
            solve(result of taking action from position)
            for action in possible actions from position
        ) 

        value = opposite_value(opponent)
        mark position as seen 
        store position's value
        return value 
```

## Deliverable

Modify or replace your solver from Part 1 to be able to solve `100-to-0-by-1-or-2` instantly (if it can't already). This will require that you implement some of the ideas in the [positional solvers](#positional-solvers) section. You should not need to change any of the code in your `zero_by` implementation.

Once that works, create a new `Game` implementation for Tic-Tac-Toe using its "physical" board configurations as position symbols. **You do not need to reduce the amount of boards in any way.** Writing tests will be very helpful. 

Finally, format and print the number of winning, losing, and tying positions under the above symbolic representation of Tic-Tac-Toe when queried. See the [checks](#checks) section for specifics.

{{< hint danger >}}

**Do not write a separate solver for Tic-Tac-Toe; use the same one for both games.**

This means that you will somehow have to turn your "symbolic representation" of a tic-tac-toe board (e.g., `[['x', 'o', 'x'], ...]`) into an `int` and vice versa. This is known as position hashing and unhashing, and we have included an [appendix](#position-hashing) with an introduction to the topic.

This can be a complicated matter, so to help you do this, we have written a [Python class](https://gist.github.com/maxfierrog/f8ca575ef1d00b1715fb90942695502f) that does this for you (but assumes a board encoding). Feel free to use it if your solution is in Python. Otherwise, you will need to write your own.

{{< /hint >}}

## Checks 

The following commands will be ran from your submission's top-level directory verbatim. We will expect to see the output shown below each of them in `STDOUT`. There will be no input provided through `STDIN`. 

{{< hint warning >}}

You will [need to read](https://docs.python.org/3/library/os.html#os.environ) the `ITEMIZED` [environment variable](https://en.wikipedia.org/wiki/Environment_variable) to determine whether or not to print a per-position breakdown of values like you did for Part 1. This is because it would be messy to print all position-to-value mappings in Tic-Tac-Toe.

{{< /hint >}}

### `100-to-0-by-1-or-2`

Command:

```
ITEMIZED=1 make run ARGS="zero_by 100 1 2"
```

Output:

```
100: TODO
99: TODO
<CUT 98 LINES FOR BREVITY>
1: WIN 
0: LOSE

WIN: TODO
TIE: TODO
LOSE: TODO
TOTAL: 101
```

### Tic-Tac-Toe

Command:

```
ITEMIZED=0 make run ARGS="tic_tac_toe"
```

Output:

```
WIN: 2836
TIE: 1068 
LOSE: 1574
TOTAL: 5478
```

## Appendix

While techniques in game abstraction help us drastically reduce the number of positions we must traverse when doing search, there is still the problem of encoding their symbols in a computer program. We will take a brief overview of how this is done by turning position symbols into numbers, and will introduce some systems problems related to this.

### Position Encoding 

Remember the [game interface](/main/assignments/homefun-pt1#the-game-interface) from Part 1. What is the concrete type `Position`? When creating systems that will be used to solve many different games, we make sure that all games agree on a single position encoding data type, which is usually a 64-bit integer. This is in contrast to making it, for example, a list of integers.

The reason we make this seemingly arbitrary choice is because having a fixed-size datatype means that a lot of the machine code needed to hande it can be streamlined at compilation time. We choose an integer datatype specifically because bitwise operations on integers are usually available, making it easy to do bit-level symbol encodings.

It is not difficult to express a position symbol for an abstract strategy game in most modern programming languages because the symbols themselves are expressed in terms of fundamental mathematical objects (e.g., grids, sets, etc.), which enjoy ample library provisions. But when given such a representation, how do we turn it into a 64-bit integer?

### Hashing and Unhashing 

There is no single answer for this, but without adding any constraints, it is also not a very hard problem. To inspire you, here is one way you could do it for Tic-Tac-Toe in a 32-bit integer:

```
AB CD EF GH IJ KL MN OP QR 00 00 00 00 00 00 00
```

Here, each letter is a bit. Each pair of letters stands for a single number (e.g., `11` is 3 in binary). There are 9 pairs of letters, one for each square in the Tic-Tac-Toe board. When there is a `00`, that will mean there is nothing in that square. When there is a `01`, there is an `X`. `10` will be an `O`. Just like that, we have created a what is known as a **hash function** for Tic-Tac-Toe positions.

A function that carries out the inverse process (in this example, turning a 32-bit integer into our representation of a Tic-Tac-Toe position) is known as an **unhashing function**. When writing a game-solving system, it is usually necessary to implement both to enable back-and-forth communication between solving algorithms and `Game` implementations. 

{{< hint info >}}

High-efficiency `Game` implementations operate on 64-bit integers directly as position representations, eliminating the need for hashing and unhashing. However, this is very unergonomic from a programming standpoint.

{{< /hint >}}


### Issues At Scale

However, there are very real constraints associated with most game-solving systems. Most of them are associated with storing very large datasets. In general, there are two options when it comes to storing a mapping of positions to any kind of value: Storing hashes or not. 

You can store a mapping of key-value pairs (with position hashes as keys) by using hash values to index into an array of values. However, this relies on the density of the hash function being relatively high (while staying collision-free), which is generally very difficult to achieve. For very sparse hashes, you end up with a mostly empty array that wastes space. This is what would happen if you used the hash function described above.

If you do store hashes, you will pay an upfront space cost of the hash size for every value you wish to store. This may seem like a small overhead, but when you consider that the values being stored are usually no more than a few bits in length, it is hard to justify using up to 90% of your space just to store hashes (which are usually 64 bits in length). Furthermore, it becomes slower to query the mapping, as you no longer have the benefit of constant-time array lookups.

{{< hint info >}}

These are only problems for bigger games. You do not need to worry about any of this for now.

{{< /hint >}}
