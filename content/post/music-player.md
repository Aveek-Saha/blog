---
title: Dusk Player
subtitle: Play music on your desktop
date: 2018-08-28
bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "JavaScript" , "Howler" , "Desktop" , "Electron" ]
---

If you want the code for this project, fork this GitHub repo - [`Music Player`](https://github.com/Aveek-Saha/MusicPlayer) <br>
If you want to see a live demo, Download it here - [`Downloads`](https://github.com/Aveek-Saha/MusicPlayer/releases)

<!--more-->

# Introduction
The idea for this music player was to keep things simple. you just load all your audio files from your music player, and start listening. The player always keeps the focus on the music.
Apart from the essential music controls, like play, pause, skip and seek, there is a playlist menu so that you can quickly switch songs.
It is built on Electron and uses Howler for handling music playback. AngularJS and Bootstrap are used for the UI, and JSmediatags to retrieve the ID3 tags, like album art, artist and title.

# Using the player
On starting the music player, you’ll see an option called folder in the top menu, click on it. Select the folder with all your music, and your set to go. the player will automatically load all the supported audio files into a playlist. It also stores the location of the directory you last opened, and it'll load your music from there when you open it next.

The UI is fairly intuitive, there are play pause and skip buttons. Clicking on the progress bar will seek playback to that position in the song. There is a volume slider to fine tune the volume. The hamburger menu button toggles the playlist. And the last button is to shuffle tracks.

# Implementation
The entire implementation is too long, so I'll just give an outline here. The full code is at [`Music Player`](https://github.com/Aveek-Saha/MusicPlayer)

The user selects the folder with music in it via Electron’s dialog module. This is used to open a directory selector, when a directory is selected it returns a path to that directory which is then passed to a function. This function then uses the path to scan the directory and store the paths of all the files with supported extensions in a playList array.
The path of the selected directory is stored in a file, which will be read in the future so that the user doesn't have to select a directory everytime.

The actual audio playback is handled through Howler, which provides us with methods like Play, Pause, Seek, Skip and volume adjustment. These methods are implemented in the Player object prototype.
The functions implemented are-

{{< highlight javascript >}}
Player.prototype = {

     play: function (index) {
         // Play the song at playlist[index]
     },

     pause: function () {
         // Pause all audio playback
     },

     skip: function (direction) {
         // skip to the next/previous/random track,
         // calls skipTo
     },

     skipTo: function (index) {
         // stop whatever song is playing and
         // play the song that has been skipped to
     },

     step: function () {
         // as the song is playing, change the timer value
         // and move the progress bar
     },

     formatTime: function (secs) {
         // by default the time is in seconds,
         // so convert it into mins:seconds format
     },

     volume: function (val) {
         // volume tuning
     },

     seek: function (time) {
         // move the song along to the place
         // the user clicked on the progressbar
     }
   }
{{< / highlight >}}

Most audio files have some kind of metadata attached to them in the form of media tags. This metadata includes things like Title, Artist, Album and Album art. 

These tags are read using JSmediatags using which we retrieve the name of the song, the artist and the album art. the album art is decoded from base64 and displayed as an image. If there are no media tags for a file, it simply displays the file name.

# Features
- Supports: mp3, opus, ogg, wav, aac, m4a, webm.
- Quickly switch tracks in the same folder, from the playlist button.
- Play tracks on shuffle.
- Loads music from the last selected directory.
- Fine tune the volume from the volume slider.
- Reads ID3 tags and displays the artist and album art (if they exist).

<!-- {{< highlight javascript >}}
{{< / highlight >}} -->
<br>
