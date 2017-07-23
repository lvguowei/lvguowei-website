+++
date = "2016-11-21T21:29:52+02:00"
title = "Master Mind in TDD"
tags = [ "TDD", "MasterMind", "uncle bob", "java" ]
categories = ["Object Oriented Design"]
+++

In one of Uncle Bob's video, he talked about this game called "Master Mind" when he was teaching Single Responsibility Principle(SRP).
After googled the game, turns out that it is actually a quite famous board game. For more information about the game, please look here -> [wiki](https://en.wikipedia.org/wiki/Mastermind_(board_game)).

The game can be played by two people. One comes up with a code, the other one tries to guess. The one who comes up with the code has to score the guesser's guess based on some rules.
The rules are the following:

1. For each exact match(the right char on the right position), give a `+`.
2. For each half match(the right char on the wrong position), give a `-`.

For example, if the code is `ACBB` and the guess is `BCAF`, then the score would be `+--`. The `+` is for the `C`, and two `-` are for `B` and `A`.

After explaining how to play this game, Uncle Bob gives a architecture that looks like this.

{{< figure src="/img/master-mind-1.jpg" >}}

This design shows that there are 3 **actors** in this program: **Customer**, **Game Designer** and **Strategist**. According to the Single Responsibility Principle, there will be one module for each actor. And the *Game Interface* module and *Strategy* module are dependent on *Game Play* module.

Here shows a more detailed design.

{{< figure src="/img/master-mind-2.png" >}}

Let's use these as a reference and create some empty classes and interfaces.

[source code version 0](https://github.com/lvguowei/MasterMindTDD/commit/50bd61a8f9a7a4eb3d9fe63d1f2dc8da46c532a4)

Since the center of the program is the game engine, let's implement enough `GameEngine` so that we can pass the test `gotItOnFirstGuess`.

[source code version 1](https://github.com/lvguowei/MasterMindTDD/commit/d72116eb3c870484c138e211cd7d0d0f6cee6d3f)

And it's not very hard to pass the test of `gotItOnSecondGuess`.

[source code version 2](https://github.com/lvguowei/MasterMindTDD/commit/82fca579aab74908c16cd578499351e2d281cc7f)

Then the test of `neverGetIt`.

[source code version 3](https://github.com/lvguowei/MasterMindTDD/commit/89a50b4965f83fcaf1132096d3164344695234c4)

Now, let's write tests for the `Guesser`.
If all the guesses are invalid, the `nextGuess` should return null.

[source code version 4](https://github.com/lvguowei/MasterMindTDD/commit/bebf02418fe686953f09c1bf650284106e579871)

If there is only one guess is valid, `nextGuess` should return that one and no more.

[source code version 5](https://github.com/lvguowei/MasterMindTDD/commit/8d0f621ad9acfa0bd05dab2fabd3b03d25a7ed2b)

Next, test how the `Guesser` generate guesses.

[source code version 6](https://github.com/lvguowei/MasterMindTDD/commit/f8f7ce8a6e76be745ca135f0e5e087498e561fe6)

Tests for the `Scorer`.

[source code version 7](https://github.com/lvguowei/MasterMindTDD/commit/4ef948e41c6d8e996435c72cd4aa3e795138cc77)

Finally, tests for `RememberingGuessChecker`.
[source code version 8](https://github.com/lvguowei/MasterMindTDD/commit/5e52719cb3324b0a10371dd52dfa522433a04780)
