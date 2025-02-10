
# Make a Game for the Tulip Creative Computer

## Contents

1. [Intro](#intro)
	- [What is the Tulip?](#what-is-the-tulip)
2. [Making a Game](#making-a-game)
	- [Game Base](#game-base)
	- [Building it out Further](#building-it-out-further)
		- [Player](#player)
		- [Game mechanics](#game-mechanics)
3. [Helpful links](#helpful-links)

---
## Intro
### What is the Tulip?

The Tulip Creative Computer is an "affordable, portable and focused device for music, writing and coding." (straight from [the Tulip's website](https://tulip.computer))

though making games is not it's *main* purpose, but it has a pretty good game API. there is also a [web editor](https://tulip.computer/run/).
## Making a Game

for this tutorial, you can (in theory) follow it on the actual hardware, but I'll be focusing on the web editor.

FOLLOW THE [GETTING STARTED GUIDE](https://github.com/shorepine/tulipcc/blob/main/docs/getting_started.md) FIRST.
you need basic knowledge of how the tulip works to be able to do this.

### Game Base

we'll be making a simple base for most games you can make. This will literally just be a player you can move around the screen.

create a new directory, I'll name it `basic_game`. (use `mkdir(basic_game)` to do it)
then, click the "show editor" button in the web UI.

Then, you can type:
```python
import tulip
```
After that, name it the same thing as your folder, (in this case, `basic_game.py`) and save it to that folder.

Ok, now,  you *should* be able to start making the actual game!

First, we'll be making the basic player movement (this will be straight from the actual website).

First, we need the boilerplate code.

type this into your game file:
```python
import tulip

def game_loop(app):
	pass

def game_setup(app):
	pass

def run(app):
	pass
```
First we're going to focus on the `game_setup` function.

download this image and put in your game's folder, with a name along the lines of "player.png".

We want a player that we can move around with the arrow keys. So, let's edit the `game_setup` function to this:
```python
def game_setup(app):
	# we want to create a player so we put:
	app.player = tulip.Player()
	# and
	app.player.load("player.png") # to load the image of the player
	app.player.on() # to turn on the player
	tulip.frame_callback(game_loop, app) # to tell the tulip to call our
	                                     # game loop function every frame
	
```
(read the comments for more explanation)

Next, we want to make the `game_loop` function we created earlier. So, write this code, above your `game_setup` function:
```python
def game_loop(app):
	app.player.joy_move() # captures arrow keys & updates player's x and y
	ap.player.move() # redraw player at it's new position
```
(read the comments, it's helpful!!)

Finally, we need to initialize the game with this code:
```python
def run(app):
	app.game = True
	app.activate_callback = game_setup
	app.present()
```
Save the file, and type `run('game_name')` (be sure to not add the `.py` at the end)

You should be able to move your player around the screen!
## Helpful Links
### color reference:

![img](https://cdn.statically.io/gh/shorepine/tulipcc/main/docs/pics/rgb332.png?raw=true)

[open in new tab](https://cdn.statically.io/gh/shorepine/tulipcc/main/docs/pics/rgb332.png?raw=true)

### Find the API reference [here](https://github.com/shorepine/tulipcc/blob/main/docs/tulip_api.md)

---