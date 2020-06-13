---
layout: post
title:  "A simple Python basketball game"
categories: misc
---
I wrote my first lines of code in early high school with the help of a book on writing computer games in Visual Basic. While I don't remember anything "playable" coming out of those early attempts, I recently started work on a computer game again -- this time in Python. 

I used the [arcade](https://arcade.academy/) framework to build a simple basketball game in which you use the mouse to aim and throw the ball. The more baskets you are able to score in 60 seconds,the better. What's really great about arcade is that it comes with lots of examples that make writing your first game easier.

Though it is really mostly a POC at this point, it already features somewhat realistic ball physics (powered by [pymunk](http://www.pymunk.org/en/latest/)) that make it surprisingly fun to play.

Here's what the game looks like:

![Example](/assets/gifs/bball_screen_capture.gif)

## Source code

If you're curious and want to try out the game for yourself, the source code is available [here](https://github.com/matsmaiwald/py_bball)