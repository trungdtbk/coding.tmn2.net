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


# Simulate the physics of the ball
