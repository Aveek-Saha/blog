---
title: Making an online chess website with Socket.io
subtitle: A tutorial on client server communication using Sockets
date: 2018-10-20
bigimg: [{src: "/img/chess-cover.jpg", desc: ""}]
tags: [ "websockets" , "NodeJS" , "JavaScript" , "Website" ]
---
 
If you want the code for this project, fork this GitHub repo - [`Online chess`](https://github.com/Aveek-Saha/Online-Chess) <br>
Check out the code in action here, play it yourself - [`Live demo`](https://chess0.herokuapp.com)
 
<!--more-->
 
# Introduction
 
In this tutorial you'll learn how to build a basic online Chess platform with multiple rooms, where people can play against each other. We will be using Sockets to communicate between the client and the server.
 
# Socket.io
From the socket.io documentation -
 
> Socket.IO is a library that enables real-time, bidirectional and event-based communication between the browser and the server.
 
Two important methods that you need know are-
 
The ```emit``` method emits an event and you can send data along with this event as long as it's JSON encodable or binary data. 
 
```
socket.emit('test', msg); 
```
 
The ```on``` method listens for an event and it can receive data that has been emitted. 
 
```
socket.on('test', (msg) => {
 console.log(msg);
});
```
<br>
 
# File Structure
 
{{< highlight javascript >}}
 
chess
   |----chessboard // A folder for the chessboard.js library
   |
   |----img // Images for the chess pieces
   |
   |--game.js // The actual game
   |--index.js // The node server
   |
   |--full.html
   |--index.html
   |
   |--package.json
 
{{< / highlight >}}
<br>
 
# Setup
 
Create a new project folder, cd to that directory and then run 
```
npm init
```
 
After that go to [`Chessboard.js`](http://chessboardjs.com/) and download the latest version. Extract the contents to the `chessboard` folder. Copy the `img` folder from `chessboard` to the root directory . We're going to use this library to display and interact with the chessboard and the pieces.
 
Now we'll create the ```index.html``` and ```game.js``` files. Go to this [`Highlight Legal Moves`](http://chessboardjs.com/examples#5003) example on the [`Chessboard.js`](http://chessboardjs.com/) website.
 
For checking valid moves, piece placement and check/checkmate detection we'll be using another library called [`Chess.js`](https://github.com/jhlywa/chess.js). We're going to include this via CDN in ```index.html```
 
Copy the JavaScript section of the code into ```game.js```. Your File should look like this.
 
```javascript
 
var board,
 game = new Chess();
 
var removeGreySquares = function() {
 $('#board .square-55d63').css('background', '');
};
 
var greySquare = function(square) {
 var squareEl = $('#board .square-' + square);
  var background = '#a9a9a9';
 if (squareEl.hasClass('black-3c85d') === true) {
   background = '#696969';
 }
 
 squareEl.css('background', background);
};
 
var onDragStart = function(source, piece) {
 // do not pick up pieces if the game is over
 // or if it's not that side's turn
 if (game.game_over() === true ||
     (game.turn() === 'w' && piece.search(/^b/) !== -1) ||
     (game.turn() === 'b' && piece.search(/^w/) !== -1)) {
   return false;
 }
};
 
var onDrop = function(source, target) {
 removeGreySquares();
 
 // see if the move is legal
 var move = game.move({
   from: source,
   to: target,
   promotion: 'q'
   // NOTE: always promote to a queen for example simplicity
 });
 
 // illegal move
 if (move === null) return 'snapback';
};
 
var onMouseoverSquare = function(square, piece) {
 // get list of possible moves for this square
 var moves = game.moves({
   square: square,
   verbose: true
 });
 
 // exit if there are no moves available for this square
 if (moves.length === 0) return;
 
 // highlight the square they moused over
 greySquare(square);
 
 // highlight the possible squares for this piece
 for (var i = 0; i < moves.length; i++) {
   greySquare(moves[i].to);
 }
};
 
var onMouseoutSquare = function(square, piece) {
 removeGreySquares();
};
 
var onSnapEnd = function() {
 board.position(game.fen());
};
 
var cfg = {
 draggable: true,
 position: 'start',
 onDragStart: onDragStart,
 onDrop: onDrop,
 onMouseoutSquare: onMouseoutSquare,
 onMouseoverSquare: onMouseoverSquare,
 onSnapEnd: onSnapEnd
};
board = ChessBoard('board', cfg);
 
```
 
 
Next up is ```index.html```.
 
```html
<!doctype html>
<html lang="en">
 
<head>
    <meta charset="UTF-8" />
    <title>Chess 0</title>
 
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.2/chess.js"></script>
    <script src="./chessboard/js/chessboard-1.0.0.js"></script>
    <link rel="stylesheet" type="text/css" href="./chessboard/css/chessboard-1.0.0.min.css">
 
</head>
 
<body>
 
    <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.1.1/socket.io.js"></script>
 
    <div class="container">
        <!-- The chess board -->
        <div id="board" style="width: 500px; margin: auto"></div>
    </div>
 
    <script src="./game.js"></script>
</body>
 
</html>
 
```
 
If you just double click on the ```index.html``` file you should be able to see a simple chessboard and the pieces. You can move the pieces around, and hovering on a piece will show you the possible moves you can make. This is because of the integration with `Chess.js` that does the move generation and valid move checking for us.
 
In case a user tries to join a game that already has 2 players in it, we'll redirect him to ```full.html```, which is just a basic html page that says the game is full.
 
```html
 
<!doctype html>
<html lang="en">
 
<head>
    <meta charset="UTF-8" />
    <title>Room Full</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css"
        integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js"
        integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy"
        crossorigin="anonymous"></script>
 
</head>
 
<body style="margin: 4%">
 
    <div id="player" style="margin: auto">This room is full</div>
</body>
 
 
 
</html>
 
```
 
# Packages
 
For this project we will be using only `Express` and `Socket.io`. To install them, run
 
```
npm install --save express
 
npm install --save socket.io
```
 
# The Server
 
Create ```index.js``` and require the following modules.
 
```javascript
const express = require('express');
const http = require('http');
const socket = require('socket.io');
```
 
Then we're going to declare a few variables
 
```javascript
 
// use 8080 as the default port number, process.env.PORT is
//useful if you deploy to Heroku
const port = process.env.PORT || 8080
 
var app = express();
 
// start the server
const server = http.createServer(app)
 
// initialize a new instance of socket.io by passing the HTTP server object
const io = socket(server)
 
// keep track of how many players in a game (0, 1, 2)
var players;
 
// create an array of 100 games and initialize them
var games = Array(100);
for (let i = 0; i < 100; i++) {
   games[i] = {players: 0 , pid: [0 , 0]};
}
 
```
 
Our routing is simple, we only need to show ```index.html``` when "/" is accessed
 
```javascript
// Add the static directory for our js and css files
app.use(express.static(__dirname + "/"));
 
app.get('/', (req, res) => {
   res.sendFile(__dirname + '/index.html');
});
 
```
 
Now its time to listen for the ```connection``` event on the server
 
```javascript
 
io.on('connection', function (socket) {
 
 // just assign a random number to every player that has connected
 // the numbers have no significance so it
 // doesn't matter if 2 people get the same number
 var playerId =  Math.floor((Math.random() * 100) + 1)
 console.log(playerId + ' connected');
 
 // if a user disconnects just print their playerID
 socket.on('disconnect', function () {
   console.log(playerId + ' disconnected');
 });
});
 
```
 
<br>
 
# The Client
 
Let's turn our attention back to the client, ```index.html```. We're going to add an input box where the player enters the room number he wants to join and once he hits a button, if the room is not full, he'll join the room.
 
After adding that and a few divs to display some information, we add bootstrap and the final ```index.html``` looks like this:
 
```html
<!doctype html>
<html lang="en">
 
<head>
    <meta charset="UTF-8" />
    <title>Chess 0</title>
 
    <!--  Adding bootstrap-->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css"
        integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js"
        integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy"
        crossorigin="anonymous"></script>
 
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.2/chess.js"></script>
    <script src="./chessboard/js/chessboard-1.0.0.js"></script>
    <link rel="stylesheet" type="text/css" href="./chessboard/css/chessboard-1.0.0.min.css">
 
    <!-- Adding some style -->
    <style>
        .number::-webkit-inner-spin-button,
        .number::-webkit-outer-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
    
        #roomNumbers {
            margin: 7px;
        }
    
        body {
            margin: 4%
        }
    </style>
 
</head>
 
<body>
 
    <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.1.1/socket.io.js"></script>
 
    <div class="container">
        <!-- The chess board -->
        <div id="board" style="width: 500px; margin: auto"></div>
 
        <div style="margin: auto"">
        <!-- Shows the player number and color -->
        <div id=" player"></div>
        <div id="roomNumbers">Enter a room number between 0 and 99</div>
        <form>
            <div class="row">
                <div class="col">
                    <input type="number" id="room" min="0" max="99" class="form-control form-control-md number">
                </div>
                <div class="col">
 
                    <!-- A button that connects the user to the given valid room number -->
                    <button id="button" class="btn btn-success" onclick="connect()">Connect</button>
                </div>
            </div>
        </form>
 
        <!-- Displays weather the game is in progress, or over -->
        <div id="state">Join Game</div>
    </div>
 
    </div>
 
    <script src="./game.js"></script>
</body>
 
</html>
```
 
Now we have to modify ```game.js``` so that it communicates with the server. After initializing the socket we can communicate via the two methods mentioned earlier.
 
We'll start off modifying ```game.js``` by adding a few variables.
 
```javascript
 
// creates a new chess game using the chess.js library
game = new Chess();
//initialize the socket
var socket = io();
// piece color
var color = "white";
// number of players in the current room
var players;
// the room number between 0 and 99
var roomId;
// if the both players have joined then it will be false
var play = true;
 
// For some DOM manipulation later
var room = document.getElementById("room")
var roomNumber = document.getElementById("roomNumbers")
var button = document.getElementById("button")
var state = document.getElementById('state')
 
```
 
<br>
 
# More Server Stuff
 
We set up the client side socket, and we will be able to communicate between the client and server. But before that we have a few more things to set up here, inside
 
```javascript
io.on('connection', function (socket) {})
```
 
We'll be emitting and receiving some events from the client here and after this, we'll set up the emitters and receivers on the client side too.
 
What we'll use the for server here is basically passing on the message from one client to the other. So in most of the ```socket.on()``` function callbacks, you'll see a ```socket.emit``` function, so that the server can pass on the event to the rest of the clients. since the server will emit events to all clients. The clients will know if the event was meant for them because we pass the ```roomId``` in the message. The ```roomId``` is checked on the client side. This method isn't secure, but it's fine for our small application.
 
This part is added in the `io.on('connection')` callback in ```index.js``` after the playerID is generated. We'll also add a new function on `disconnect` to free up room space when players disconnect.
 
```javascript
 
var color; // black or white
 
// 'joined' is emitted when the player enters a room number and clicks
// the connect button the room ID that the player entered gets passed as a message
 
socket.on('joined', function (roomId) {
 // if the room is not full then add the player to that room
 if (games[roomId].players < 2) {
     games[roomId].players++;
     games[roomId].pid[games[roomId].players - 1] = playerId;
 } // else emit the full event
 else{
     socket.emit('full', roomId)
     return;
 }
  console.log(games[roomId]);
 players = games[roomId].players
  // the first player to join the room gets white
 if (players % 2 == 0) color = 'black';
 else color = 'white';
 
 // this is an important event because, once this is emitted the game
 // will be set up in the client side, and it'll display the chess board
 socket.emit('player', { playerId, players, color, roomId })
 
});
 
// The client side emits a 'move' event when a valid move has been made.
socket.on('move', function (msg) {
 // pass on the move event to the other clients
 socket.broadcast.emit('move', msg);
});
 
// 'play' is emitted when both players have joined and the game can start
socket.on('play', function (msg) {
 socket.broadcast.emit('play', msg);
 console.log("ready " + msg);
});
 
// when the user disconnects from the server, remove him from the game room
socket.on('disconnect', function () {
 for (let i = 0; i < 100; i++) {
     if (games[i].pid[0] == playerId || games[i].pid[1] == playerId)
         games[i].players--;
 }
 console.log(playerId + ' disconnected');
 
});
 
```
<br>
 
# Back to the Client
 
After setting up our server to relay messages back and forth from clients we set up the client to respond to the server emitted events. So let's get back to ```game.js```.
 
First thing we'll do is set up a listener for the 'player' event, which means that a player has joined the event and the board will be set up, along with the player's color. The code we wrote to create the board in the first step also goes in this.
 
Put this at the bottom of ```game.js```, after removing the old `cfg` and `board` variables.
 
```javascript
 
socket.on('player', (msg) => {
 
 var plno = document.getElementById('player')
 
 // we're passing an object -
 // { playerId, players, color, roomId } as msg
 color = msg.color;
 
 // show the players number and color in the player div
 players = msg.players;
 plno.innerHTML = 'Player ' + players + " : " + color;
 
 // emit the play event when 2 players have joined
 if(players == 2){
   play = false;
   // relay it to the other player that is in the room
   socket.emit('play', msg.roomId);
   // change the state from 'join room' to -
   state.innerHTML = "Game in Progress"
 }
 // if only one person is in the room
 else
   state.innerHTML = "Waiting for Second player";
 
 
 var cfg = {
   orientation: color,
   draggable: true,
   position: 'start',
   onDragStart: onDragStart,
   onDrop: onDrop,
   onMouseoutSquare: onMouseoutSquare,
   onMouseoverSquare: onMouseoverSquare,
   onSnapEnd: onSnapEnd
 };
 board = ChessBoard('board', cfg);
});
 
var board;
```
 
 
Now we need to modify some of the functions that we copied from the example at [`chessboardjs`](http://chessboardjs.com/examples#5003).
 
For ```onDragStart``` we add a few more conditions to check if the move is valid or not. The conditions we added are -
 
```
play || // check if both players have joined
 
// if the player is white, he cannot move black pieces and vice versa
(game.turn() === 'w' && color === 'black') ||
(game.turn() === 'b' && color === 'white')
```
<br>
Add these extra checks to the function.
 
```javascript
 
var onDragStart = function (source, piece) {
 
 // A few more rules have been added
 if (game.game_over() === true || play ||
     (game.turn() === 'w' && piece.search(/^b/) !== -1) ||
     (game.turn() === 'b' && piece.search(/^w/) !== -1) ||
     (game.turn() === 'w' && color === 'black') ||
     (game.turn() === 'b' && color === 'white') ) {
         return false;
 }
};
```
 
The function ```onDrop```, is when the pieces are actually moved. So when the player makes a move and drops a move, the 'move' event is emitted.
 
```javascript
 
var onDrop = function (source, target) {
 removeGreySquares();
 
 // see if the move is legal
 var move = game.move({
     from: source,
     to: target,
     promotion: 'q'
 });
 if (game.game_over()) {
     state.innerHTML = 'GAME OVER';
     socket.emit('gameOver', roomId)
 }
 
 // illegal move
 if (move === null) return 'snapback';
 
 // if the move is allowed, emit the move event.
 else
     socket.emit('move', { move: move, board: game.fen(), room: roomId });
 
};
 
```
 
 
Now we need to add some listeners, so that changes that are sent via events are reflected in the client.
 
```javascript
 
// if the room is full (players > 2), redirect the user
// to the full.html page we made earlier
socket.on('full', function (msg) {
 if(roomId == msg)
   window.location.assign(window.location.href+ 'full.html');
});
 
// change play to false when both players have
// joined the room, so that they can start playing
// (when play is false the players can play)
socket.on('play', function (msg) {
 if (msg == roomId) {
     play = false;
     state.innerHTML = "Game in progress"
 }
});
 
// when a move happens, check if it was meant for the clients room
// if yes, then make the move on the clients board
socket.on('move', function (msg) {
 if (msg.room == roomId) {
     game.move(msg.move);
     board.position(game.fen());
     console.log("moved")
 }
});
 
```
 
One last method requires implementation, the ```connect``` function. When we click the connect button it needs to emit an event with the room id that the player has entered.
 
```javascript
 
var connect = function(){
 
 // extract the value of the input field
 roomId = room.value;
 // if the room number is valid
 if (roomId !== "" && parseInt(roomId) <= 100) {
   room.remove();
   roomNumber.innerHTML = "Room Number " + roomId;
   button.remove();
 
   // emit the 'joined' event which we have set up a listener for on the server
   socket.emit('joined', roomId);
 }
}
 
```
 
# Checking if it works
 
For the complete code, check the repo, [`Online chess`](https://github.com/Aveek-Saha/Online-Chess).
 
cd to the project directory, and then run
 
```
node index.js
```
 
If you've done everything correctly you'll see something like this [`Live demo`](https://chess0.herokuapp.com) here. You can open up as many instances as you want and try playing with your friends.
 
<br>
<!-- {{< highlight javascript >}}
{{< / highlight >}} -->



