
# Make a Game for the Tulip Creative Computer

## Contents

1. [Intro](#intro)
	- [What is the Tulip?](#what-is-the-tulip)
2. [Making a Game](#making-a-game)
	- [Game Base](#game-base)
	- [Building it out Further](#building-it-out-further)
		- [collisions](#collisions)
		- [Finishing up](#finishing-up)
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
import tulip, random

(w,h) = tulip.screen_size() # we'll need this later

def game_loop(app):
	pass

def game_setup(app):
	pass

def run(app):
	pass
```
First we're going to focus on the `game_setup` function.

Download this image and put in your game's folder, with a name along the lines of "player.png":
![image](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAYAAABzenr0AAACAElEQVRYR7VXC5bDIAiM9v4nbmt2RqLBP2Z37WuaKoFhQDDuOI4TXwx/uCOkPzKlhsd9mPy/l9aSLhrNVw8AWnVlmXL8RhGPT+hIizIxXQNofCkm5ElezouIufy9SkC09XA4R9NkPQP44PZVqMs2iC1J8vcPhgCgWg8GYOk8vx2XOm42UxvokigNAwAJNIdgxvhwTRmsSbsZgPNE8p3mgDJhij2EArTqHCmewx5wssifk2vTJOx4MnGunyHVAwUDtl0gGsYErKgpETwAYE/9CGWBZwBg5UULog2FTce/MmDhqgEQsAtcLIpSIAIc8b1Kx8INkbyUqvhmVdxkYELrsDDVPNzO8c57VF3sP3MhSuq2t98gHmsGBpbK6UrIln9SgHIveNoNlWcahrVODAEMKWbDQqLZmuE8UDEHzAxsdyFblhQMxAOPakbTULJzoIOwh0tDnYydbvgGgM2tbKk3bHPx/FeHzr0wQ1+ihOqGDWgLoxvZL6jBnpPTV1sHVsqu9YirJ6sB53sleM39ahtaSEnxGfmjAMixvDmQxMBcHBminUCtCJQA4Phr3oZbSW7jZgmgq8biWga7Fi5zAKl4hsS5ge9KZG0u7rMink0S8lRc1gEDnQvLMw3bu8Di5dBgZ4FJSJ25DsyIl9fNkeiTV9JkjRXyejWzR9749mtwjSI/xknyB0T4ugsAAAAASUVORK5CYII=)

We want a player that we can move around with the arrow keys. So, let's edit the `game_setup` function to this:
```python
def game_setup(app):
	# we want to create a player so we put:
	tulip.bg_rect(0, 0, w, h, 29, 1)
	app.player = tulip.Player()
	# and
	app.player.load(app.app_dir + "/player.png") # to load the image of the player
	app.player.on() # to turn on the player
	app.player.x = 30 # change x
	app.player.y = 30 # and y
	app.player.move() # and move there
	
	tulip.frame_callback(game_loop, app) # to tell the tulip to call our
	                                     # game loop function every frame
	
```
(read the comments for more explanation)

After that, we need to make a function to revert everything that we changed.

type this below `game_setup`:
```python
def game_teardown(app):
    tulip.frame_callback()
```

Next, we want to make the `game_loop` function we created earlier. So, write this code, above your `game_setup` function:
```python
def game_loop(app):
	app.player.joy_move() # captures arrow keys & updates player's x and y
	app.player.move() # redraw player at it's new position
```
(read the comments, it's helpful!!)

Finally, we need to initialize the game with this code:
```python
def run(app):
	app.game = True # tell the tulip that this is a game
	app.activate_callback = game_setup # tell the tulip what to do when it opens the game
	game.deactivate_callback = game_teardown # tell the tulip what to do when the game is quitted or switched away from
	app.present()
```
Save the file, and type `run('game_name')` (be sure to not add the `.py` at the end)

You should be able to move your player around the screen!

### Building it out further
#### Collisions

Now, we need collisions. So, let's add an item for the player to pick up first.

We need an image for our item, but I'll just be using the same image as the player.

Add this code to `game_setup`:
```python
def game_setup(app):
	# player stuff here
	
	app.item = tulip.Sprite() # basically the same stuff we did with the player
	app.item.load(app.app_dir + "/player.png", 32, 32)
	app.item.on()
	app.item.x = 60
	app.item.y = 60
	app.item.move()

	tulip.frame_callback(game_loop, app) 
```
now, if you run it, you *should* see the item.

The item is pretty boring right now. It doesn't do *anything*. we should add collisions.

with tulip, checking collisions is pretty straightforward. basically the `tulip` package has a `collisons()` function that returns an array with tuples of two numbers. The two numbers are the two sprite ids that are colliding. 

But how do we add that to our code?

Type this into your `game_loop` function below what you already have in there.
```python
def game_loop(app):
	# rest of the code up here

	for c in tulip.collisions()
		(a, b) = c
		print(f"collision: {a} -> {b}")

```
Now, if you run it again, whenever you walk into the item, when you close the game, you should see something along the lines of `collision: 0 -> 1`. If you see numbers higher than that, you probably have a problem.

Now we can actually move the item!

type this into `game_loop`:
```python
def game_loop(app):
	# rest of the code up here

	for c in tulip.collisions()
		(a, b) = c
		print(f"collision: {a} -> {b}")
		if (a==0 and b==1):
			app.item.x = random.randrange(w)
			app.item.y = random.randrange(h)
			app.item.move()
```
What we do here is we check for the collision between the player and the item, and move the item to a new location.

#### Finishing up
Now, let's add a score, timer and lose condition to complete the game loop.

First we'll need a `Stats` class (you don't, but it makes everything a **whole** lot easier).

Type this into your code, below the `game_loop` function.
```python
class Stats:
	def __init__(self):
		self.score = 0
		self.timer = 30
		self.timer_done = False

	def draw(self):
		tulip.bg_roundrect(0,0,50,40,5,34,1)
		tulip.bg_str(str(self.score), 10, 15, 255, 5)
		tulip.bg_str(str(self.timer), 10, 30, 255, 5)

	def inc_score(self):
		self.score += 1
		self.draw()

	def dec_timer(self, t):
		self.timer -= 1
		self.draw()
		if self.timer > 0 and not self.timer_done:
			tulip.defer(self.dec_timer, None, 1000)
```
What I do here is in `__init__` I set the score to 0, and the timer to 30, and tell `Stats` that the timer isn't done. Next, in `draw`, I draw the score. I'm not going to go in depth on the `tulip.bg` functions, because the API reference (linked at the bottom) does a way better job of explaining it. After that, in `inc_score` I increment the score by 1 and then draw the score, reflecting the changes. Finally, in `dec_timer`, I decrement the timer by one, and then redraw the score and timer, and then it checks if the timer is still running and if the score is greater than 0, and if it is, we schedule the tulip to run the function 1 second (1000 milliseconds) in the future.

Now we need to actually use it, so put this into your `game_setup`:
```python
def game_setup(app):
	# rest of code up here, minus the frame_callback.
	app.stats = Stats()
	app.stats.draw()
	tulip.defer(app.stats.dec_timer, None, 1000)

	tulip.frame_callback(game_loop, app)
```
What we do here is we draw the stats, then schedule the timer decrement 1 second in the future.

Now we need to ensure the game doesn't try to draw the timer (and score) even when it's been quitted.

In order to do that, edit your `game_teardown` to look like this:
```python
def game_teardown(app):
	tulip.frame_callback()
	app.stats.timer_done=True
```
All we do here is tell stats that the timer is done if we quit the game early, or switch away from it.

Finally, we need to add a couple things to our `game_loop`.

edit your `game_loop` function to look like this:
```python
def game_loop(app):
    app.player.joy_move()
    app.player.move()
    
    for c in tulip.collisions():
        (a, b) = c
        
        print(f"collision: {a} -> {b}")
        
        if (a==0 and b==1 and app.stats.timer > 0):
            app.item.x = random.randrange(w)
            app.item.y = random.randrange(h)
            app.item.move()
            app.stats.inc_score()
    
    if app.stats.timer <= 0:
        tulip.bg_str(f"Good job! you got {app.stats.score}!", int(w/2-100), int(h/2), 0, 12)
  
```
What we change here is we add an extra condition to moving the item (and increment the score when you get it), and then we add a game over message when the score reaches zero.

with that, you should have a fully playable game that you *should* be able to run it.

if you need it, this is the full code:
```python
import tulip, random

(w, h) = tulip.screen_size()


#TIMER_DONE=False

def game_loop(app):
    app.player.joy_move()
    app.player.move()
    
    for c in tulip.collisions():
        (a, b) = c
        
        print(f"collision: {a} -> {b}")
        
        if (a==0 and b==1 and app.stats.timer > 0):
            app.item.x = random.randrange(w)
            app.item.y = random.randrange(h)
            app.item.move()
            app.stats.inc_score()
    
    if app.stats.timer <= 0:
        tulip.bg_str(f"Good job! you got {app.stats.score}!", int(w/2-100), int(h/2), 0, 12)
    
    
class Stats:
    def __init__(self):
        self.score = 0
        self.timer = 30
        self.TIMER_DONE=False
        
    def draw(self):
        tulip.bg_roundrect(0,0,50,40,5,34,1)
        tulip.bg_str(str(self.score),10, 15, 255, 5)
        tulip.bg_str(str(self.timer),10, 30, 255, 5)
    
    def inc_score(self):
        self.score += 1
        self.draw()
    
    def dec_timer(self, t):
        
        self.timer -= 1
        self.draw()
        if self.timer > 0 and not self.TIMER_DONE:
            tulip.defer(self.dec_timer, None, 1000)

def game_setup(app):
    tulip.bg_rect(0, 0, w, h, 29, 1)
    
    app.player = tulip.Player()
    app.player.load(app.app_dir + "/imgs/player.png", 32, 32)
    app.player.on()
    app.player.x = 30
    app.player.y = 30
    app.player.move()

    app.item = tulip.Sprite()
    app.item.load(app.app_dir +  "/imgs/player.png", 32, 32)
    app.item.on()
    app.item.x = 60
    app.item.y = 60
    app.item.move()
    
    app.stats = Stats()
    app.stats.draw()
    tulip.defer(app.stats.dec_timer, None, 1000)
    
    tulip.frame_callback(game_loop, app)

def game_teardown(app):
    tulip.frame_callback()
    app.stats.TIMER_DONE=True
        
def run(app):
    app.game = True
    #app.bg_color = 29
    app.activate_callback = game_setup
    app.deactivate_callback = game_teardown
    app.present()
```

Thank you for bearing with me, reading this, and making your first game for the tulip!

if you have any questions about, or possible improvements with the tutorial, and you are in the Hack Club slack, join `#tulipcc-ysws` and ask it there.

if you aren't in the Hack Club slack, open an issue on github.

to try my example, (same exact code, use `world.download('collector')` in the repl to download the game, and then use `run('collector`) to run it.)
## Helpful Links
### color reference:

![img](https://cdn.statically.io/gh/shorepine/tulipcc/main/docs/pics/rgb332.png?raw=true)

[open in new tab](https://cdn.statically.io/gh/shorepine/tulipcc/main/docs/pics/rgb332.png?raw=true)

### Find the API reference [here](https://github.com/shorepine/tulipcc/blob/main/docs/tulip_api.md)

---