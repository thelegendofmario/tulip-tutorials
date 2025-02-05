
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

The Tulip Creative Computer is an "affordable, portable and focused device for music, writing and coding."^[straight from [the Tulip's website](https://tulip.computer)]

though making games is not it's *main* purpose, but it has a pretty good game API. there is also a [web editor](https://tulip.computer/run/).
## Making a Game

for this tutorial, you can (in theory) follow it on the actual hardware, but I'll be focusing on the web editor.
### Game Base

If you want to skip to making the actual game (and skip boilerplate) copy the code in [this](https://tulip.computer/run/?fn=game_base.py&share=H4sIAAAAAAAAA51QSw6DIBBdl1PMEpLGA5iwbrpuuiYUR0OKSvn0/A1iLUbddDnzPvPe6N6OLkCIRltClJHew0X2SKdNdb/elEMc0orVBACgwRaE0IMOQlCPpmU1OZ18tOgoqxaEEZKYnexRmHG0tPtZWOl9hl9RB6GkMQ%2bpntRPt2ZSHqokqxLt6yhV0G8Z8ECWc7cu3V0YS4pzaTsbNviP5Sx2cTiMDTy/kk1AiW4OAt/2KgWrPwFf/60k7pQBvldxk8k69DgEyj5Eu7ohEwIAAA==) editor. **I recommend copying the code into your own instance of the editor (without the share part of the link), just so if you reload the page, you won't lose your progress (if you happened to not save).** Then, skip the next section, and go straight to [Building it out Further](#building-it-out-further)

Before we get started, try to play around a little with the editor/tulip. Then, while in the tulip window, type `mkdir('your_game_name_here')` into the repl. Then, type `cd('your_game_name_here')` into the repl. Then in the editor above the tulip window, rename the file to the same name as your game folder, with a `.py` at the end of it. After that, you are ready to start making your game!

First, we need to import the `tulip` package. This has all of the API goodies we need.

```python
import tulip
```

Next, we need to make the class for our game.

```python
class Game(tulip.UIScreenGame):

    def __init__(self):
        super().__init__()
```

We create a class with the `class` command, pretty sef explanatory, we name it `Game`, and the we have it inherit the `tulip.UIScreenGame` class, which takes care of some of the boilerplate. After that we create the `__init__` function, and make it call the `tulip.UIScreenGame`'s `__init__` function.

  

  

Next, we need to add a game loop.

  

```python
# game class definition up above here ^^
def game_loop(game):
    pass
```

(right now this won't do anything, but I promise it'll become very important :) )

Then, we need to add the callbacks needed by the tulip to control the app.

```python

  

# previous code up here

  

def quit_callback(screen):
    screen.game.quit()

  

def activate_callback(screen):
    tulip.frame_callback(game_loop, screen.game)

  

def deactivate_callback(screen):
    tulip.frame_callback()

```

What we do there is we create the functions for the callbacks that are going to be called by the tulip when certain things happen. The `quit_callback` function is for when the game is quit, and just tells the game to quit. The `activate_callback` is for when the game first starts, *and* for when we switch back onto the game while it is running. What we do is we make the tulip call the `game_loop` function we called earlier with the screen.game argument as the `game` parameter. Finally, the `deactivate_callback` is for when the game is switched away from on the tulip.

Next, we have to actually tell the tulip that we want it to run *our* callback functions.

```python

# this should be the very last function
# in your program

def run(screen):
    screen.game = Game()
    screen.activate_callback = activate_callback
    screen.quit_callback = quit_callback
    screen.deactivate_callback = deactivate_callback
    screen.present()
```

What we do here is we initialize the game with `screen.game = Game()`, then we tell the app what it's callback functions are, and the present the game. Note: every app and game made for the tulip needs a `run(screen)` function and inside that to use `screen.present()`.

With that, your code should look like this:

```python
import tulip

class Game(tulip.UIScreenGame):

    def __init__(self):
        super().__init__()

def game_loop(game):
    pass

def quit_callback(screen):
    screen.game.quit()

def activate_callback(screen):
    tulip.frame_callback(game_loop, screen.game)

def deactivate_callback(screen):
    tulip.frame_callback()

def run(screen):
    screen.game = Game()
    screen.activate_callback = activate_callback
    screen.quit_callback = quit_callback
    screen.deactivate_callback = deactivate_callback
    screen.present()
```

run it by saving it, (clicking the red arrow underneath the code editor after selecting your project folder in the `user` directory), and then run it by clicking on the tulip window and typing `run('gamenamehere')`. (be sure to not include the `.py` in the function.)
### Building it out Further
#### Player

we could stop here, but where's the fun in that? We are going to make a simple game where you run around collecting something to give you points, and you need to get as many as you can, before the time runs out.

So, First, we need to change the background color.

Change the `run(screen)` function to

```python
def run(screen):
    screen.game = Game()
    screen.bg_color = 29
    screen.activate_callback = activate_callback
    screen.quit_callback = quit_callback
    screen.deactivate_callback = deactivate_callback
    screen.present()
```

`screen.bg_color = 29` can go anywhere *after* `screen.game = Game()`, but before `screen.present()`, but putting right there is good for organization.

Next we need a player. find a pixel art image that suits your wants for your player (I recommend 32x32 size). I'm using this:

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAYAAABzenr0AAAAAXNSR0IArs4c6QAAAEFJREFUWEft17ERADAMwkCx/9DOpckIwYWYAL4jwFBM1hSY+QuR3O3wBCyggAIKKKCAAgoooIACCihQF2gd5Po7PtYgdAHJdK73AAAAAElFTkSuQmCC)

(right click, save and upload it to the tulip web editor)

Now, we can start coding the player.

```python
class Game(tulip.UIScreenGame):
    def __init__(self):
        super().__init__()
        self.player = tulip.Player()
        self.player.load("image.png", 32, 32)
        self.player.on()
```

What we do here is we create the player variable, then we set the play'ers image to "image.png" of width 32 and height 32. (replace `image.png` with the name of the image you uploaded and the width and height fo the image you uploaded as well, in pixels). Then, we tell the tulip to start showing the player with `self.player.on()`.

If you run it again, you'll see the player in the top left corner! But it doesn't move yet. Let's fix that.

change your `game_loop` function to this:

```python
def game_loop(game):
    game.player.joy_move()
```

`joy_move()` is a helper function and checks for the arrow keys being pressed and changed the x and y of the sprite accordingly. Although, if run it now, you won't be able to move the sprite at all.

You need to add an extra line of code:

```python
def game_loop(game):
    game.player.joy_move()
    game.player.move()
```

`game.player.move()` just updates the sprites position to it's x and y coordinates.

  

Now your code should look like this:

```python
import tulip

class Game(tulip.UIScreenGame):
    def __init__(self):
        super().__init__()
        self.player = tulip.Player()
        self.player.load("download (1).png", 32, 32)
        self.player.on()
        
def game_loop(game):
    game.player.joy_move()
    game.player.move()

def quit_callback(screen):
    screen.game.quit()

def activate_callback(screen):
    tulip.frame_callback(game_loop, screen.game)

def deactivate_callback(screen):
    tulip.frame_callback()

def run(screen):
    screen.game = Game()
    screen.bg_color = 29
    screen.activate_callback = activate_callback
    screen.quit_callback = quit_callback
    screen.deactivate_callback = deactivate_callback
    screen.present()  
```

  

I you run it now, you *should* be able to move the player around with the arrow keys.
#### Game Mechanics
## Helpful Links
### color reference:

  

![img](https://cdn.statically.io/gh/shorepine/tulipcc/main/docs/pics/rgb332.png?raw=true)

[open in new tab](https://cdn.statically.io/gh/shorepine/tulipcc/main/docs/pics/rgb332.png?raw=true)

### Find the API reference [here](https://github.com/shorepine/tulipcc/blob/main/docs/tulip_api.md)