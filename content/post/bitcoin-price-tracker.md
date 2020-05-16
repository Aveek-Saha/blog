---
title: Bitcoin Price Tracker
subtitle: Get notified about Bitcoin prices
date: 2018-08-05
#bigimg: [{src: "/img/wordSearch.png", desc: "Screenshot"}]
tags: [ "JavaScript" , "Axios" , "Desktop", "Electron" ]
---

If you want the code for this project, fork this GitHub repo - [`Bitcoin Price Tracker`](https://github.com/Aveek-Saha/Bitcoin-price-tracker) <br>
If you want to see a live demo, Download it here - [`Downloads`](https://github.com/Aveek-Saha/Bitcoin-price-tracker/releases)

<!--more-->

## Introduction
This application was something of a test run, to get familiar with the Electron  framework . For those of you that haven’t heard of it, Electron is a framework that allows you to build cross platform desktop apps with JavaScript, HTML, and CSS.

Full disclosure, this was made by following a great tutorial by Gary Simon. Do check it out, he does a good job of explaining the basics and anyone with some experience in web development should be able to pick it up with no problem.

## Details
I wont go into too much technical detail here, because the tutorial already does that far better than I could, but the basic idea is to make a  simple desktop application for Linux, that notifies you when the Bitcoin price exceeds a limit set by you. Unfortunately, the notifications will only work in Linux and Mac OS, but not Windows because the notification system for non system apps was broken after the last update.

The application uses axios to get the current bitcoin price from cryptocompare. Then we create a function called getBTC, which fetches the bitcoin price and then compares it to the value entered by the user. This function is executed every 10 seconds via setInterval. If the bitcoin price is found to be greater than the user value, a desktop notification is sent.

The user enters the Price limit via another window, and the value entered by the user is sent back to the main window via the ipc.renderer. 

<br>