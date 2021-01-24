---
layout: post
title:  "Prisoners' Dilemma Tournament"
categories: misc
---

I finally got around to working on something that I had wanted to code up ever since I first studied game theory in undergrad: __a tournament of lots of different agents playing repeated prisoner's dilemma against each other__. The tournament is run with many agents, all of which start with a random, (and for now) static response function (i.e. they don't learn over time) with the aim to understand what the winning response (or policy) function is. The tournament does not operate in a simple one-on-one knockout fashion -- in which case we know that the typical strategies of a _(defect, defect)_ Nash Equilibrium would win, but tries to approximate [Robert Axelrod's](https://en.wikipedia.org/wiki/Robert_Axelrod) famous tournaments, by after each round eliminating all players that have accumulated below average points. The key here is that it will allow for cooperation to be a viable strategy: a pair of players that cooperates will be more likely to advance to the next round than a pair that does not cooperate.

Similar to Axelrod's tournaments, the winning strategy is often a _tit-for-tat_ strategy. If you'd like to play around with the parameters of the game to see how they affect the outcome, you can download the code from my github [here](https://github.com/matsmaiwald/prisoners_dilemma).

Apart from the fun of implementing the tournament itself, it was also nice to apply a couple of __new software engineering practices I've picked up__ such as:
-   using json-files to store code configuration and have the correctness of the config file be verified by [pydantic](https://pydantic-docs.helpmanual.io/) schemas
-   leveraging the advantages of object-oriented programming (e.g. having a class for player objects)   
-   automatically generating documentation with [sphinx](https://www.sphinx-doc.org/en/master/) and [autodoc](https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html) and having it hosted on readthedocs -- checkout the docs [here](https://prisoners-dilemma.readthedocs.io/en/latest/)

