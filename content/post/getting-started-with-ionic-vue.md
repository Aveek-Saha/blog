---
title: Getting started with Ionic 5 and Vue
subtitle: A beginners guide on how to build a mobile application with Ionic and Vue
date: 2022-11-10
# bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "Ionic" , "Vue" , "GitHub" , "Website" ]
---

If you want the code for this project, fork this GitHub repo - [`Movie Vue`](https://github.com/Aveek-Saha/MovieVue/) <br>
Download the app here - [`Download`](https://github.com/Aveek-Saha/MovieVue/releases/)

<!--more-->

# Introduction

Javascript has come a long way from when it used to be just a way to make web pages more dynamic, now we can use it to write server side code and even make desktop and mobile applications. The later is what what we'll be exploring today, using [`Ionic`](https://ionicframework.com/) and [`Vue.js`](https://vuejs.org/). 

In this tutorial we'll be focusing on learning how to use `Ionic`, and it assumes you have basic knowledge of `Vue` concepts. If you want to learn `Vue` check out this tutorial: [`Getting started with Vue and the GitHub API`](https://home.aveek.io/blog/post/getting-started-with-vuejs-and-the-github-api/), where I go over all the basic concepts in `Vue`.

## What is Ionic?

From the ionic team:
> Ionic is an open source mobile UI toolkit for building high quality, cross-platform native and web app experiences.

Ionic will provide us with UI components that will look at home on mobile platforms, and function virtually indistinguishably from any native app.

Ionic gives you a choice between Angular, React, Vue, or vanilla JavaScript. However, note that not all components are available for all frameworks, so take a look at all the components you want to use and check if they're available for your framework before you start.

With Ionic you can also use native device functionality, like accessing the camera or using the location on a mobile service, though take note that this service is a part of their enterprise edition.

## What are we building?

We'll be building a mobile application where users can see a list of movies that are currently playing, popular movies, top rated movies and new movies that are going to release.

App Features:
1. A slide out menu from where you can see the different movie lists, Now Playing, Popular, Upcoming, or Top Rated.
1. Each movie will have a background image, the title, the average rating of the movie and the description.
1. Infinite scroll, once you reach the end of the page, new content automatically loads.
1. Swipe down on any page to refresh it.
1. Show region specific movies

To get our movies, we'll be using the [`TMDb api`](https://developers.themoviedb.org/3/movies/get-now-playing) and to get our location we'll be using [`ip-api`](http://ip-api.com/json/).

You'll need an API key for the `TMDb api` before we start, so make sure to grab that.

## Final Result

![Movie Vue Demo](/ionicvue/screen.gif)


# Setup

### Install the ionic cli

```
npm install -g @ionic/cli
```

### Start an ionic app

We'll be using the Side menu starter and we'll set our framework of choice to Vue

```
ionic start MovieVue sidemenu --type vue
```

### Serve for live reload

Run this command, and it'll start a live server so you can see your changes live while you're coding.
```
ionic serve
```

When viewing the page, I'd recommend using the dev tools on your browser to set your device to a mobile device, that way you'll get a more realistic picture of what it's going to look like on an actual phone.

# Code

Upon serving, your app will look something like this

![Screenshot 1]()

![Screenshot 2]()

Most of our changes will be in the `./src` folder

### Organizing the Side menu

First thing we'll do is clean up the side menu. We need only 4 sections in the side menu, and we can get rid of all the labels.

These changes have to be made to the `./src/App.vue`.

We'll only be modifying the template and the script here, so you can leave the styles unchanged. 

Well need to rename the tabs to match the content we'll be showing. The icons are a little mismatched too, so let's fix that while we're at it.

```vue
<template>
  <IonApp>
    <IonSplitPane content-id="main-content">
      <ion-menu content-id="main-content" type="overlay">
        <ion-content>
          <ion-list id="inbox-list">
            <!-- Change name to something more appropriate -->
            <ion-list-header>Movie Vue</ion-list-header>
            <ion-note>Discover movies</ion-note>
  
            <ion-menu-toggle auto-hide="false" v-for="(p, i) in appPages" :key="i">
              <ion-item @click="selectedIndex = i" router-direction="root" :router-link="p.url" lines="none" detail="false" class="hydrated" :class="{ selected: selectedIndex === i }">
                <ion-icon slot="start" :ios="p.iosIcon" :md="p.mdIcon"></ion-icon>
                <ion-label>{{ p.title }}</ion-label>
              </ion-item>
            </ion-menu-toggle>
          </ion-list>
          <!-- Remove Labels -->
        </ion-content>
      </ion-menu>
      <ion-router-outlet id="main-content"></ion-router-outlet>
    </IonSplitPane>
  </IonApp>
</template>

<script lang="ts">
import { IonApp, IonContent, IonIcon, IonItem, IonLabel, IonList, IonListHeader, IonMenu, IonMenuToggle, IonNote, IonRouterOutlet, IonSplitPane } from '@ionic/vue';
import { defineComponent, ref } from 'vue';
import { useRoute } from 'vue-router';
// Update the icons
import {  heartOutline, heartSharp, flashOutline, flashSharp, diamondOutline, diamondSharp, rocketOutline, rocketSharp } from 'ionicons/icons';

export default defineComponent({
  name: 'App',
  components: {
    IonApp, 
    IonContent, 
    IonIcon, 
    IonItem, 
    IonLabel, 
    IonList, 
    IonListHeader, 
    IonMenu, 
    IonMenuToggle, 
    IonNote, 
    IonRouterOutlet, 
    IonSplitPane,
  },
  setup() {
    const selectedIndex = ref(0);
    // Rename the tabs and update the icons
    const appPages = [
      {
        title: 'Now Playing',
        url: '/folder/Now Playing',
        iosIcon: flashOutline,
        mdIcon: flashSharp
      },
      {
        title: 'Popular',
        url: '/folder/Popular',
        iosIcon: heartOutline,
        mdIcon: heartSharp
      },
      {
        title: 'Top Rated',
        url: '/folder/Top Rated',
        iosIcon: diamondOutline,
        mdIcon: diamondSharp
      },
      {
        title: 'Upcoming',
        url: '/folder/Upcoming',
        iosIcon: rocketOutline,
        mdIcon: rocketSharp
      }
    ];
    // Remove Labels
    
    const path = window.location.pathname.split('folder/')[1];
    if (path !== undefined) {
      selectedIndex.value = appPages.findIndex(page => page.title.toLowerCase() === path.toLowerCase());
    }
    
    const route = useRoute();
    
    return { 
      selectedIndex,
      appPages,
      // Update the icons
      heartOutline,
      heartSharp,
      flashOutline,
      flashSharp,
      diamondOutline,
      diamondSharp,
      rocketOutline,
      rocketSharp,
      isSelected: (url: string) => url === route.path ? 'selected' : ''
    }
  }
});
</script>
```

Now we need to update the script at `.src/router/index.ts` to change the default route. This will ensure it opens the page we want when it starts up.

```ts
import { createRouter, createWebHistory } from '@ionic/vue-router';
import { RouteRecordRaw } from 'vue-router';

const routes: Array<RouteRecordRaw> = [
  {
    path: '',
    // Change the default route
    redirect: '/folder/Now Playing'
  },
  {
    path: '/folder/:id',
    component: () => import ('../views/Folder.vue')
  }
]

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
})

export default router

```
After these changes it should look something like this

![Screenshot 3]()





# Build
ionic build

ionic cap add android


<!-- For next tutorial -->

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
