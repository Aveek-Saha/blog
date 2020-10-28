---
title: Making the Chrome dinosaur game with Phaser
subtitle: Create a clone of the classic chrome game
date: 2018-10-11
bigimg: [{src: "/img/runny.png", desc: "Screenshot"}]
tags: [ "Phaser" , "games" , "JavaScript", "website" ]
---

If you want the code for this project, fork this GitHub repo - [`Dinosaur game code`](https://github.com/Aveek-Saha/Chrome-dinosaur-game-clone) <br>
Check out the code in action here, play it yourself - [`Live demo`](https://aveeksaha.gitlab.io/runny/)

<!--more-->

# Introduction
Chrome has this really simple and fun game that you can play when your WiFi is down. It consists of a small dinosaur that has to jump over cacti. This game has a couple of core mechanics, a player that jumps when the up key is pressed, and an infinite number of randomly generated obstacles for the player to jump over. 

Both these things can be easily achieved in Phaser, which is a 2D game framework for making HTML browser games. We're going to create a game with almost the same mechanics but with a different look. 

I'm going to call it ```Runny```

If you're new to phaser I would suggest reading a getting started tutorial.

# File Structure

{{< highlight javascript >}}
runny
    |---assets // A folder for all our tiles and sprites
    |
    |---js // All the javascript files
    |   |--boot.js
    |   |--gameover.js
    |   |--gametitle.js
    |   |--main.js
    |   |--phaser.min.js
    |   |--preload.js
    |
    |--index.html // Where everything comes together
{{< / highlight >}}

For this project we'll only be using `preload.js` and `main.js` from the `js folder`

The boilerplate code was taken from [`Joshua Morony's website`](https://www.joshmorony.com/) , download the files [`Here`](https://www.joshmorony.com/wp-content/uploads/2015/03/phaser-template.zip)

# Assets
We need three basic kinds of tiles- the ground, the player and the obstacles. I'll be using 2 different png files for the obstacles and the ground. For the player I'm going to use a sprite sheet because it can be animated.

The ground and obstacle tiles were Made by [`Kenny`](http://kenney.nl/assets?q=2d) and the player sprite sheet was made by [`arks`](https://arks.itch.io/dino-characters).

# Preload.js
Here we load the assets that we need before we use them. In preload.js, change 
```this.game.state.start("GameTitle");``` to
```this.game.state.start("Main");```
<br>
Then add the following
{{< highlight javascript >}}
preload: function(){

    // ground tile
    this.game.load.image('tile', 'assets/tile.png'); 
    // obstacle tile
    this.game.load.image('box', 'assets/box.png'); 
    // player sprite
    this.game.load.spritesheet('player', 'assets/player.png', 24, 24, 9, -2); 
}
{{< / highlight >}}<br>

# Main.js
This is where the fun stuff happens!<br>

### Create
In the create function we initialize a few variables that we'll be using later.

{{< highlight javascript >}}
this.tileVelocity = -450; // velocity of the obstacles
this.rate = 1500; // rate at which the obstacles appear on screen
this.jumping = false; // is the player jumping?

// add keyboard input
this.cursors = this.game.input.keyboard.createCursorKeys(); 

// set a blue background color
this.game.stage.backgroundColor = '479cde'; 

// start the phaser arcade physics system
this.game.physics.startSystem(Phaser.Physics.ARCADE); 

// execute addObstacles at the rate we set 
this.timer = game.time.events.loop(this.rate, this.addObstacles, this); 


{{< / highlight >}}

Now we need 2 groups, one for the ground and one for the obstacles <br>

{{< highlight javascript >}}

// the floor group
this.floor = this.game.add.group();
this.floor.enableBody = true;
this.floor.createMultiple(Math.ceil(this.game.world.width / this.tileWidth), 'tile');

// the obstacle group
this.boxes = this.game.add.group();
this.boxes.enableBody = true;
this.boxes.createMultiple(20, 'box');
this.game.world.bringToTop(this.floor)

this.addBase(); // add the ground for the player to run on
this.createPlayer(); // add the player to the game

{{< / highlight >}}

`Final create function`
{{< highlight javascript >}}
create: function() {

    this.tileVelocity = -450;
    this.rate = 1500;
    this.jumping = false;

    this.tileWidth = this.game.cache.getImage('tile').width;
    this.tileHeight = this.game.cache.getImage('tile').height;	
    this.boxHeight = this.game.cache.getImage('box').height;

    this.game.stage.backgroundColor = '479cde';

    this.game.physics.startSystem(Phaser.Physics.ARCADE);

    this.floor = this.game.add.group();
    this.floor.enableBody = true;
    this.floor.createMultiple(Math.ceil(this.game.world.width / this.tileWidth), 'tile');

    this.boxes = this.game.add.group();
    this.boxes.enableBody = true;
    this.boxes.createMultiple(20, 'box');
    this.game.world.bringToTop(this.floor)

    this.addBase();
    this.createPlayer();
    this.cursors = this.game.input.keyboard.createCursorKeys(); 

    this.timer = game.time.events.loop(this.rate, this.addObstacles, this);

	},
{{< / highlight >}}<br>

### Add Base
Now our player will need a platform to run on. The ground is actually not going to move, so we can just set a fixed number of tiles based on the size of the screen. Let's add the base.

{{< highlight javascript >}}
addBase: function () {

    // calculate how many tiles are needed
    var tilesNeeded = Math.ceil(this.game.world.width / this.tileWidth); 

    // the tiles should be at the bottom of the screen
    var y = (this.game.world.height - this.tileHeight); 

    for (var i = 0; i < tilesNeeded; i++) {

        // add one tile after the other
        var x = i * this.tileWidth; 
        var tile = this.floor.getFirstDead();
        tile.reset(x, y); // set the x and y coordinates
        tile.body.immovable = true;

    }
}
{{< / highlight >}}<br>

### Create the Player
Since the player has something to stand on now, we can go ahead and create the player. 

{{< highlight javascript >}}

createPlayer: function () {

    // spawn the player a to the left and a little above the ground
    this.player = this.game.add.sprite(this.game.world.width/5, this.game.world.height -(this.tileHeight*2), 'player'); 

    // depends on the size of your sprite
    this.player.scale.setTo(4, 4); 
    this.player.anchor.setTo(0.5, 1.0);

    // enable arcade physics on the player
    this.game.physics.arcade.enable(this.player); 

    // the player has to fall down once it jumps
    this.player.body.gravity.y = 2200; 
    this.player.body.bounce.y = 0.1;
    this.player.body.drag.x = 150;

    // since it is a sprite sheet, you can set animations
    var walk = this.player.animations.add('walk'); 

    // play the walk animation at 20fps
    this.player.animations.play('walk', 20, true); 
}

{{< / highlight >}}<br>

### Add the Obstacles
Just having the player jump without anything to jump over would be pretty boring, so we're going to add some boxes. In case you haven't realized already, we haven't given the player a velocity. This is because the player isn't actually going to move, we can simply create the illusion of movement by giving the obstacles a velocity in the direction of the player.

Also to keep things interesting, the height of the obstacles the player has to jump over is random and based on that the player can either do a single jump or a double jump. We'll implement the jumping function later.

{{< highlight javascript >}}
addObstacles: function () {

    // Randomly decide how tall the stack of boxes is going to be
    // maximum number of tiles that the player can jump over is 4
    var tilesNeeded = Math.floor( Math.random() * (5 - 0));
    
    // slowly increase the difficulty by increasing how often boxes spawn and how fast they move
    if (this.rate > 200) {
        this.rate -= 10;
        this.tileVelocity = -(675000 / this.rate);
    }

    // Add the boxes to the game
    for (var i = 0; i < tilesNeeded; i++) {
        // we want the boxes to be created just outside the right side of the screen
        this.addBox(this.game.world.width , this.game.world.height - this.tileHeight - ((i + 1)* this.boxHeight ));
    }
}

{{< / highlight >}}

The function above doesn't actually add the boxes to the map, that's done by ```addBox```, which creates a box at the given x and y coordinates.

{{< highlight javascript >}}

addBox: function (x, y) {

    // get the boxes that have already been moved outside the screen
    var tile = this.boxes.getFirstDead();

    tile.reset(x, y);
    // set the velocity of the set of boxes
    tile.body.velocity.x = this.tileVelocity;
    tile.body.immovable = true;
    tile.checkWorldBounds = true;
    // destroy them when they go outside the screen
    tile.outOfBoundsKill = true;
}

{{< / highlight >}}<br>

### Movement
Now we need to give the player a way to actually jump over the boxes when we press the UP arrow. This is taken care of in the ```update``` function which can constantly check for input. We'll also be implementing a double jump mechanism that we talked about earlier.
This isn't a part of the original game, but makes it more interesting. We'll also be checking for collisions between the other tiles and the player.If the player touches a box, the game is over.

{{< highlight javascript >}}
update: function() {

    // collide with the floor
    this.game.physics.arcade.collide(this.player, this.floor);
    // collide with the boxes and call gameOver when the player hits a box
    this.game.physics.arcade.collide(this.player, this.boxes, this.gameOver, null, this);

    // implementing the double jump
    var onTheGround = this.player.body.touching.down;

    // If the player is touching the ground, let him have 2 jumps
    if (onTheGround) {
        this.jumps = 2;
        this.jumping = false;
    }

    // Jump!
    if (this.jumps > 0 && this.upInputIsActive(5)) {
        this.player.body.velocity.y = -1000;
        this.jumping = true;
    }

    // Reduce the number of available jumps if the jump input is released
    if (this.jumping && this.upInputReleased()) {
        this.jumps--;
        this.jumping = false;
    }

}
{{< / highlight >}}

There are 2 more methods we need to implement, one for checking if the up arrow is held down, and one to check if it's released.

{{< highlight javascript >}}

// This function returns true when the player presses the "jump" control
upInputIsActive: function (duration) {
    var isActive = false;

    isActive = this.input.keyboard.downDuration(Phaser.Keyboard.UP, duration);
    isActive |= (this.game.input.activePointer.justPressed(duration + 1000 / 60) &&
        this.game.input.activePointer.x > this.game.width / 4 &&
        this.game.input.activePointer.x < this.game.width / 2 + this.game.width / 4);

    return isActive;
},

// This function returns true when the player releases the "jump" control
upInputReleased: function () {
    var released = false;

    released = this.input.keyboard.upDuration(Phaser.Keyboard.UP);
    released |= this.game.input.activePointer.justReleased();

    return released;
}

{{< / highlight >}}<br>


# Game Over
For the last part of our project, we need to do something when the player hits a box and the game is over. In this case since we don't have a Start Screen we'll just load up the game again, i.e ```main.js```
So put this line in ```gameOver```

{{< highlight javascript >}}
this.game.state.start('GameOver');
{{< / highlight >}}

<br>

