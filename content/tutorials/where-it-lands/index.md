---
title: "Where It Lands"
description: ""
date: 2022-04-24T19:23:03+12:00
lastmod: 2022-04-24T19:23:03+12:00
draft: false
images: []
---

In this tutorial we're going to create a game call **Where It Lands**. This is a quite simple game to play but creating it may not be as easy as playing it. Let's get started.

# How to Play

**Where It Lands** is a memory game. It has a ball or something at the top of the screen which is falling down when the game starts. At the bottom, there are several hoops or cups. In the between are blocks that will change the direction of the ball. The player needs to remember the layout of the blocks which are shown very briefly and predict where the ball will hit. Do you get it?


# Design the Game

The first step in designing the game is to describe how it looks like to the player. When the player hits the `when flag clicked`, we will display a **message** introducing what the game is all about. The message could be like **How good is your memory. Let test it.**. Below the message is a button **START** that the player clicks to start the game.

The START button is clicked, we can display a screen to select the the difficulty of the game. However, this is an **optional** feature. We want it to make the game more interesting and challenging but we don't need it to make the game work.

When the START button is clicked, the ball will fall down straight to the ground if no block changes its direction. We need to simulate a bit of physics here that if the ball hits a block in an angle, it will follow the block to the end of its before continuing falling. It will do the same if it hits another block.


{{< figure src="./ball-physics.png" >}}

We only show the blocks for very short time, like 1/2 second. We also want the blocks to display in a different layout each time. We will need to code the logic for this.

We also need to know which cup the play has selected. To select a cup the play simply click on the cup. Once the cup is selected, we need to know where the ball lands so we can check if the play is correct or wrong.


In summary, we need:
1. To simulate the physics of the ball falling down
1. To range the blocks differently each time
1. To display the blocks in very short time
1. To know which cup the play has selected
1. To know which cup the ball hit

Let implement it.

# Simulate the physics of the ball

First, create a Sprite for the ball and call it **Ball**. Let move the ball to the top of the screena and make it fall down. This simple algorithm would do it (we call it Fall algorithm):

```
point in direction (180)
repeat until <touching (edge v)>
  move (5) steps
  wait (0.01)
```

Second, create a Sprite for the block, called it **Block 1** and draw a rectangle. Using the block `point in direction ()` to set the block an angle of 120 or -120.

Now we need to make sure the ball will follow the block if it hits one. To do this, we need to know when the ball hits the block and the direction of the block. Let say the block have color **green**.
To keep the direction of the block, create a variable called: `(Block Direction)`.

Implement this algorithm in the Block 1 sprite:

```
when flag clicked
forever
  if <touching (Ball v)> then
    set [Block Direction v] to (direction)
  end
end
```

Third, in the Ball sprite, expand the Fall algorithm like below:

```
point in direction (180)
repeat until <touching (edge v)>
  if <touching color [#00ff00]> then
    point in direction (Block Direction)
  else
    point in direction (180)
  end
  move (5) steps
  wait (0.0001)
```

Now test if it works. You can test it by clicking on the Flag and then click on the group of blocks above to activate them. You should see the ball falls and then follows the block when it hits it like below:

{{< figure src="./ball-physics-in-action.gif" >}}

# Make random arrangement of the blocks

Now we have the Ball physics and a block. We want to have more blocks with many different arrangement to make the game more interesting and challenging.

You can simply duplicate the blocks and move them to different places. But that would mean the player see the same thing every time.

You can use the `go to x: (pick random (-180) to (180)) y: (-80)` but it is very hard to know where the ball will fall. We need to make the ball fall into several places so we can put the cups/hoops in.

A simple way to do this is to make a list and put the places you want to block to be in and select the place randomly. See below:

```
when flag clicked
delete all of [Block Places v]
add (-120) to [Block Places v]
add (0) to [Block Places v]
add (120) to [Block Places v]
```

You can change the places or add more whatever you want. Then we can control the block where it with the blocks below. Put them into the Block sprite.

```
when flag clicked
go to x: (item (pick random (1) to (3)) of [Block Places v] )) y: (-80)
```

We also want to control where the ball flows to the left or right. This can be done by using a list called **Block Angles**.

```
when flag clicked
delete all of [Block Angles v]
add (-120) to [Block Angles v]
add (120) to [Block Angles v]
```

Then very similar to the place, we could do this:

```
when flag clicked
go to x: (item (pick random (1) to (3)) of [Block Places v] )) y: (-80)
point in direction (item (pick random (1) to (2)) of [Block Angles v])
```

Once done, the result should be like this:

{{< figure src="./block-arrangement.gif" >}}

What you need to do now is to duplicate this Block sprite to make some more of them at different layers, like top, middle and bottom, similar to this:

{{< figure src="./block-layout.png" >}}

# Implement the cups

Create some Cup sprites (because they are the same, make one first and duplicate to make more). Place it at the bottom of the screen. You can do several tests to see where the ball would fall, then place the cup there.

You need to know if the player has selected a cup and which one is this. You can do so by numbering the cup, like 1,2,3,4. Then make a variable called `(Selected Cup)` and use the event `when this sprite clicked` to change its value to the cup number. For example, in the Cup 1 sprite:

```
when this sprite clicked
  set [Selected Cup v] to (1)
```

You need to know which cup the ball hit so you can score the player. This is simple enough with the following blocks in Cup 1 sprite and a variable called `(Correct Cup)`.

```
when flag clicked
forever
if <touching (Ball v)> then
  set [Correct Cup v] to (1)
```

Now to score the player, you just need to compare if the `(Selected Cup)` equals the `(Correct Cup)`.


Now the main logic of the game is done. Let put the blocks togther to complete the game. I let you figure it out. A version of the game is available [here](https://scratch.mit.edu/projects/673946603/)
