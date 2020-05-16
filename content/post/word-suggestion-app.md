---
title: Word Suggestion App
subtitle: Find what you're looking for
date: 2018-03-04
#bigimg: [{src: "/img/wordSearch.png", desc: "Screenshot"}]
tags: [ "C programing" , "JavaScript" , "PHP" , "Web Development", "Website" ]
---

We often find ourselves grasping at a word that’s on the tip of our tongue but we just can’t pin it down, or wanting to check the spelling of a word that we’re not sure of.

<!--more-->

Since this is such a common problem, I decided to use My front end knowledge and data structures to come up with a solution. This project has 2 parts.

<!-- {{< figure src="/img/wordSearch.png" caption="Screenshot" caption-position="bottom" caption-effect="fade" >}} -->

# The C part:
I made a C program that uses a list of words, to build a trie, which is a kind of search tree. When this program runs, and an input word is given, it displays a list of words that are close or similar to it. 
There is also a spell check mode that will display if a word is  correctly spelled or not. This program also creates a text file of the most frequently spelt incorrect words. Once this was done, I moved on the the front end part.

# The web interface:
Using php’s exec function I piped the output of this program to a PHP array. This PHP file is then asynchronously called when the user presses “alt +h”. Then I added some CSS to make the whole thing look nice.

# The final features features include:
Two modes of operation that can be toggled-
A word suggestion mode- Where the user can type in a partially completed word and the app will suggest predictions.
A spell check mode- Where the user can enter a word and check if the spelling is correct, if it is not the app also suggests words that may contain the correct spelling of the word that the user was trying to type.
The app keeps a track of the the 5 most frequently misspelt words
The word of the day (the most frequently incorrectly spelt word) can be seen from the spelling.php page
I used an Apache server to host this application.

If you want the code for this project, fork this Github repo - https://github.com/Aveek-Saha/Autocorrect-and-spellcheck-webapp <br>
If you want to see a live demo, check this out – https://autocorrect-and-spellcheck.herokuapp.com/
Note: For the live demo I’m taking the input directly from the word list in php by storing the txt file in an array, this is because the ‘exec’ function does not work on the server.

<br>