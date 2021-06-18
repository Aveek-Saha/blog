---
title: Getting started with Ionic 5 and Vue
subtitle: A beginners guide on how to build a mobile application with Ionic and Vue
date: 2020-11-10
# bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "Ionic" , "Vue" , "GitHub" , "Website" ]
---

If you want the code for this project, fork this GitHub repo - [`Movie Vue`](https://github.com/Aveek-Saha/MovieVue/) <br>
Download the app here - [`Download`](https://github.com/Aveek-Saha/MovieVue/releases/)

<!--more-->

# Introduction

Javascript has come a long way from when it used to be just a way to make web pages more dynamic, now we can use it to write server side code and even make desktop and mobile applications. The later is what what we'll be exploring today, using [`Ionic`](https://ionicframework.com/) and [`Vue.js`](https://vuejs.org/). 

In this tutorial we'll be focusing on learning how to use `Ionic`, and it assumes you have basic knowledge of `Vue` concepts. If you want to learn `Vue` check out this tutorial: [`Getting started with Vue and the GitHub API`](https://home.aveek.io/blog/post/getting-started-with-vuejs-and-the-github-api/), where I go over all the basic concepts in `Vue`.

# What is Ionic?

From the ionic team:
> Ionic is an open source mobile UI toolkit for building high quality, cross-platform native and web app experiences.

Ionic will provide us with UI components that will look at home on mobile platforms, and function virtually indistinguishably from any native app.

Ionic gives you a choice between Angular, React, Vue, or vanilla JavaScript. However, note that not all components are available for all frameworks, so take a look at all the components you want to use and check if they're available for your framework before you start.

With Ionic you can also use native device functionality, like accessing the camera or using the location on a mobile service, though take note that this service is a part of their enterprise edition.

# What are we building?

We'll be building a mobile application where users can see a list of movies that are currently playing, popular movies, top rated movies and new movies that are going to release.

App Features:
1. A slide out menu from where you can see the different movie lists, Now Playing, Popular, Upcoming, or Top Rated.
1. Each movie will have a background image, the title, the average rating of the movie and the description.
1. Infinite scroll, once you reach the end of the page, new content automatically loads.
1. Swipe down on any page to refresh it.
1. Show region specific movies

To get our movies, we'll be using the [`TMDb api`](https://developers.themoviedb.org/3/movies/get-now-playing) and to get our location we'll be using [`ip-api`](http://ip-api.com/json/).


### Result

{{ <video controls src="/ionicvue/screenrecord.mp4" type="video/mp4"> </video> }}



1. Install ionic cli
2. Start an iconic app
```
npm install -g @ionic/cli
ionic start MovieVue sidemenu --type vue
```

3. cd into it and run
ionic serve

4. do coding

5. Build
ionic build

ionic cap add android

6. Download android studio
Use standard install type 

ionic cap open android

wait for gradle to sync

open SDK tools if needed

Build, then run

Icons in the app > res > mipmap folder

select image using path

build > Build bundles/ apk > Build Apk

build under path

MovieVue\android\app\build\outputs\apk\debug
