---
title: "Tower of Hanoi Game"
description: ""
date: 2022-06-21T07:01:40+12:00
lastmod: 2022-06-21T07:01:40+12:00
draft: false
images: []
---

In this tutorial we're gonna create Tower of Hanoi Game. The gameplay is simple: the player has to move a number of plates from one column to another making sure that no bigger plate is placed on top of smaller ones. The aim is to move those plates with minimum moves. See more about the game [here](https://en.wikipedia.org/wiki/Tower_of_Hanoi).

We're going to create a Game with 4 Plates and 3 Columns.

## Game Design

Let break down what we need to complete. There are a few logics we need to implement for the game:

1. We need to create an User Interface (UI) so that the player can move the plates around
2. We also need to make sure that the user can only move the plate on top and cannot place big plate on top of smaller ones
3. We need to check if the player has moved all plates into the last column
4. (optional) We can count how many moves the player used. The less move, the better the player is.

{{< figure src="./design.png" >}}

## Implementation

### Create UI

#### 1. Create 3 Columns
In this game we need three columns. I create a Sprite for each column and call them Column1, Column2 and Column3. Because we later need to put the plates into those columns, I create three Variables: `(Column1-X-Position)`, `(Column2-X-Position)`, `(Column3-X-Position)` which tells me where is the column in the X axis.

When the game starts, we need to set it up (e.g. set values for the variables, put plates in the first columns, etc), so I create a Sprite without Costume called **Game Init** to help me do the setup. Put this code to the Game Init sprite:

```
when flag clicked
  set [Column1-X-Position v] to (-135)
  set [Column2-X-Position v] to (0)
  set [Column3-X-Position v] to (135)
  broadcast (Game has started)
```

Then for each of the Columns, put this code to move them to the correct position:
```
when I receive [Game has started v]
  go to x:(Column1-X-Position) y:(-70)
```
Make sure to change the Variable Column1-X-Position to the correct one for each column. Now test the code to see if the columns are at positons you are happy with.


#### 2. Create Plates

Create a new Sprite and call it **Plate1**. Draw appropriate Costume for it.
We need to know if one plate is bigger or smaller than the other. I use a Variable called `(PlateX-ID)` to tell me which one is which. For Plate1, its **Plate1-ID** has value 1. Plate2 has `set [Plate2-ID v] to (2)` and so on. Plate with bigger ID is bigger.

Put this code to Plate1:
```
When flag clicked
  set [Plate1-ID v] to (1)
```

##### Place plates into Columns
Here comes the hard part. How do we know which columns have which plates. To do that, I create 3  **List** variables one for each column, called `(Plates-of-Column1)` `(Plates-of-Column2)` and `(Plates-of-Column3)`.

Now, come back to the Game Init and initialize those variable. We want the Plate1 to be in the first column (Column1). Extend the blocks of Game Init so it looks like this:

```
when flag clicked
  set [Column1-X-Position v] to (-135)
  set [Column2-X-Position v] to (0)
  set [Column3-X-Position v] to (135)
  delete all of [Plates-of-Column1 v]
  delete all of [Plates-of-Column2 v]
  delete all of [Plates-of-Column3 v]
  add (Plate1-ID) to [Plates-of-Column1 v]
  broadcast (Game has started)
```

Run the test by clicking on the Green flag and check if the List variable `(Plates-of-Column1)` has 1 in it.

Now, the Column1 has Plate1. We need to add code that will move the Plate1 into correct position.
Let see how this works. If a Column has a single plate, the plate will be at the bottom of the Column. If it has two Plates, then the last Plate takes the bottom position, the first Plate takes the next to bottom position. See image.

{{< figure src="./plate-position.png" >}}

There is a bit of Math here. The position of a Plate is computed by this formula: `((4) - (n)) + (i)`, where *n* is the number of plates in a column and *i* is the index of the plate in that column.

Let see the scenario 1 in the Image where there is a single Plate1 in the Column2, then its position is: `((4) - (1))+(1)` which is **4**. In the second scenario, the Plate 1 position is: `((4)-(2))+(1)` which is **3** and Plate 2 position is: `((4)-(2))+(2)` which is **4**.

Now, let code. I create a List variable called **Plate-Positions** and set it as below in the Game Init sprite.


```
when flag clicked
  set [Column1-X-Position v] to (-135)
  set [Column2-X-Position v] to (0)
  set [Column3-X-Position v] to (135)
  delete all of [Plates-of-Column1 v]
  delete all of [Plates-of-Column2 v]
  delete all of [Plates-of-Column3 v]
  add (Plate1-ID) to [Plates-of-Column1 v]
  delete all of [Plate-Positions v]
  add (-114) to [Plate-Positions v]
  add (-126) to [Plate-Positions v]
  add (-138) to [Plate-Positions v]
  add (-150) to [Plate-Positions v]
  broadcast (Game has started)
```


Now, open the Plate1 sprite and add code to move its to the correct position. To work out **n** which is the number of plates in Column 1, we use this block: `length of [Plates-of-Column1 v]`. To know the index **i** of Plate 1 in Column 1, we use: `item # of (Plate1-ID) in [Plates-of-Column1 v]`. The magic number **4** is actually the length of `(Plate-Positions)`.
 So the formula can be coded as below:

```
( (length of [Plate-Positions v]) - (length of [Plates-of-Column1 v]) ) + (item # of (Plate1-ID) in [Plates-of-Column1 v])
```

```
When I receive [Game has started v]
  forever
    if <[Plates-of-Column1] contains (Plate1-ID)?> then
      go to x:(Column1-X-Position) y:(( (length of [Plate-Positions v]) - (length of [Plates-of-Column1 v]) ) + (item # of (Plate1-ID) in [Plates-of-Column1 v]))
```

Now we test it to see if it works. You should expect to see the Plate1 is moved to the Column 1 at this step. Now extend the code for other columns as well. The finaly code looks like below:

```
When I receive [Game has started v]
  forever
    if <[Plates-of-Column1] contains (Plate1-ID)?> then
      go to x:(Column1-X-Position) y:(( (length of [Plate-Positions v]) - (length of [Plates-of-Column1 v]) ) + (item # of (Plate1-ID) in [Plates-of-Column1 v]))
    else
    if <[Plates-of-Column2] contains (Plate1-ID)?> then
      go to x:(Column2-X-Position) y:(( (length of [Plate-Positions v]) - (length of [Plates-of-Column2 v]) ) + (item # of (Plate1-ID) in [Plates-of-Column2 v]))
    else
      if <[Plates-of-Column3] contains (Plate1-ID)?> then
        go to x:(Column3-X-Position) y:(( (length of [Plate-Positions v]) - (length of [Plates-of-Column3 v]) ) + (item # of (Plate1-ID) in [Plates-of-Column3 v]))
    end
    end
```

##### Make sure the player cannot move any plate except the first

Scratch has an option called **drag mode**. If we `set drag mode [draggable v]` then the Sprite can be moved and if we `set drag mode [not draggable v]` then it can't be moved. We're going to use this feature and check if the Plate is the first, then make it draggable, otherwise non-draggable.

In the Plate1 sprite, add blocks to make the code look like below:

```
When I receive [Game has started v]
  forever
    if <[Plates-of-Column1] contains (Plate1-ID)?> then
      go to x:(Column1-X-Position) y:(( (length of [Plate-Positions v]) - (length of [Plates-of-Column1 v]) ) + (item # of (Plate1-ID) in [Plates-of-Column1 v]))
    else
    if <[Plates-of-Column2] contains (Plate1-ID)?> then
      go to x:(Column2-X-Position) y:(( (length of [Plate-Positions v]) - (length of [Plates-of-Column2 v]) ) + (item # of (Plate1-ID) in [Plates-of-Column2 v]))
    else
      if <[Plates-of-Column3] contains (Plate1-ID)?> then
        go to x:(Column3-X-Position) y:(( (length of [Plate-Positions v]) - (length of [Plates-of-Column3 v]) ) + (item # of (Plate1-ID) in [Plates-of-Column3 v]))
    end
    end
    if < <(item # of (Plate1-ID) in [Plates-of-Column1 v])=(1)> or < <(item # of (Plate1-ID) in [Plates-of-Column2 v])=(1)> or <(item # of (Plate1-ID) in [Plates-of-Column3 v])=(1)>  >> then
      set drag mode [draggable v]
    else
      set drag mode [non draggable v]
    end
```


##### Make the plate movable between Columns

When the player drags a plate and places into another column, the plate should move to that column.
We implement a simple logic like this:

```
if < touching (Column1 v)?> then
  insert (Plate1-ID) at (1) of [Plates-of-Column1 v]
end
```
We use **insert** instead of **add** because we want the plate to be put on top, not the bottom. However, there are problems with the code. The plate can be at different Columns at the same time, so we need to remove it from the current Column before inserting to the new one. Extend the codes as below:

```
if < touching (Column1 v)?> then
  delete (item # of (Plate1-ID) in [Plates-of-Column1 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column2 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column3 v])
  insert (Plate1-ID) at (1) of [Plates-of-Column1 v]
end
```

This block `delete (item # of (Plate1-ID) in [Plates-of-Column1 v])` only removes the Plate1 if it exists in the column 1. So we can repeat for all other columns, we don't really need to know which column the Plate1 is in currently.

There is still a problem though. What if the new Column already has plates which are smaller than the plate the play is moving? We can't allow the player to place bigger plate on top of the smaller.

The player can only move if the plate is smaller or the Column is empty. Implement this logic like below:

```
if < touching (Column1 v)?> then
  if < <(Plate1-ID)<(item (1) of [Plates-of-Column1 v])  > or < (length of [Plates-of-Column1 v])=(0)>  > then
  delete (item # of (Plate1-ID) in [Plates-of-Column1 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column2 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column3 v])
  insert (Plate1-ID) at (1) of [Plates-of-Column1 v]
  end
end
```
Now extend the code for other Columns as well:

```
if < touching (Column1 v)?> then
  if < <(Plate1-ID)<(item (1) of [Plates-of-Column1 v])  > or < (length of [Plates-of-Column1 v])=(0)>  > then
  delete (item # of (Plate1-ID) in [Plates-of-Column1 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column2 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column3 v])
  insert (Plate1-ID) at (1) of [Plates-of-Column1 v]
  end
end
if < touching (Column2 v)?> then
  if < <(Plate1-ID)<(item (1) of [Plates-of-Column2 v])  > or < (length of [Plates-of-Column2 v])=(0)>  > then
  delete (item # of (Plate1-ID) in [Plates-of-Column1 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column2 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column3 v])
  insert (Plate1-ID) at (1) of [Plates-of-Column2 v]
  end
end
if < touching (Column3 v)?> then
  if < <(Plate1-ID)<(item (1) of [Plates-of-Column1 v])  > or < (length of [Plates-of-Column3 v])=(0)>  > then
  delete (item # of (Plate1-ID) in [Plates-of-Column1 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column2 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column3 v])
  insert (Plate1-ID) at (1) of [Plates-of-Column3 v]
  end
end
```

We need to make this this code runs all the time, so it can check the plate movement as soon as the play drags the plate. The finally code look like this:

```
When flag clicked
forever
if < touching (Column1 v)?> then
  if < <(Plate1-ID)<(item (1) of [Plates-of-Column1 v])  > or < (length of [Plates-of-Column1 v])=(0)>  > then
  delete (item # of (Plate1-ID) in [Plates-of-Column1 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column2 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column3 v])
  insert (Plate1-ID) at (1) of [Plates-of-Column1 v]
  end
end
if < touching (Column2 v)?> then
  if < <(Plate1-ID)<(item (1) of [Plates-of-Column2 v])  > or < (length of [Plates-of-Column2 v])=(0)>  > then
  delete (item # of (Plate1-ID) in [Plates-of-Column1 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column2 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column3 v])
  insert (Plate1-ID) at (1) of [Plates-of-Column2 v]
  end
end
if < touching (Column3 v)?> then
  if < <(Plate1-ID)<(item (1) of [Plates-of-Column1 v])  > or < (length of [Plates-of-Column3 v])=(0)>  > then
  delete (item # of (Plate1-ID) in [Plates-of-Column1 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column2 v])
  delete (item # of (Plate1-ID) in [Plates-of-Column3 v])
  insert (Plate1-ID) at (1) of [Plates-of-Column3 v]
  end
end
```

Now, test it to see if it works as expected. If not fix it before moving on.


#### Add more Plates
We need to add three more plates. This is now simple. We just need to duplicate the Plate1 and add more variables Plate2-ID, Plate3-ID and Plate4-ID. Replace the Plate1-ID with the correct plate ID in those new Sprites.

Remember to add Plate2, Plate3 and Plate4 into the Game Init blocks:


```
when flag clicked
  set [Column1-X-Position v] to (-135)
  set [Column2-X-Position v] to (0)
  set [Column3-X-Position v] to (135)
  delete all of [Plates-of-Column1 v]
  delete all of [Plates-of-Column2 v]
  delete all of [Plates-of-Column3 v]
  add (Plate1-ID) to [Plates-of-Column1 v]
  add (Plate2-ID) to [Plates-of-Column1 v]
  add (Plate3-ID) to [Plates-of-Column1 v]
  add (Plate4-ID) to [Plates-of-Column1 v]
  delete all of [Plate-Positions v]
  add (-114) to [Plate-Positions v]
  add (-126) to [Plate-Positions v]
  add (-138) to [Plate-Positions v]
  add (-150) to [Plate-Positions v]
  broadcast (Game has started)
```



## Check if the player has won

The logic is simple. If the Column3 has all 4 plates, then the player has won. Put the code below to the Column3 sprite:

```
when flag clicked
forever
  if <(length of [Plates-of-Column3 v])=(4)> then
    say (You won!!!)
  end
```

You can replace the block **say** with something more interesting.

See the final game [here](https://scratch.mit.edu/projects/707133821/editor/)
