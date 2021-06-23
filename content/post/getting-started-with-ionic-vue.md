---
title: Getting started with Ionic 5 and Vue
subtitle: A beginners guide on how to build a mobile application with Ionic and Vue
date: 2021-03-18
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


{{< gallery >}}
{{< figure link="/ionicvue/screenshot1.png" caption="Page" thumb="-thumb">}}
{{< figure link="/ionicvue/screenshot2.png" caption="Side menu" thumb="-thumb">}}
{{< /gallery >}}

Most of our changes will be in the `./src` folder

### Organizing the Side menu

First thing we'll do is clean up the side menu. We need only 4 sections in the side menu, and we can get rid of all the labels.

These changes have to be made to the `./src/App.vue`.

We'll only be modifying the template and the script here, so you can leave the styles unchanged. 

Well need to rename the tabs to match the content we'll be showing. The icons are a little mismatched too, so let's fix that while we're at it.

```html
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
              <ion-item @click="selectedIndex = i" router-direction="root" 
              :router-link="p.url" lines="none" detail="false" class="hydrated" 
              :class="{ selected: selectedIndex === i }">
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
import { IonApp, IonContent, IonIcon, IonItem, IonLabel, IonList, IonListHeader, 
IonMenu, IonMenuToggle, IonNote, IonRouterOutlet, IonSplitPane } from '@ionic/vue';
import { defineComponent, ref } from 'vue';
import { useRoute } from 'vue-router';
// Update the icons
import {  heartOutline, heartSharp, flashOutline, flashSharp, 
diamondOutline, diamondSharp, rocketOutline, rocketSharp } from 'ionicons/icons';

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
      selectedIndex.value = appPages
      .findIndex(page => page.title.toLowerCase() === path.toLowerCase());
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

{{< figure link="/ionicvue/screenshot3.png" caption="Side menu" thumb="-thumb">}}

### Basic UI

For our main UI we'll be using a card component that will contain all the information about the movies.

Create a new file `./src/MovieCard.vue`

```html
<template>
    <ion-card>
        <!-- Movie poster at the top of the card -->
        <img :src="url" />
        <ion-card-header>
            <!-- Show the movie title, Average votes and description -->
            <ion-card-title>{{ movie.title }}</ion-card-title>
            <ion-card-subtitle
                >Rating: {{ movie.vote_average }}
                <ion-icon :icon="star"></ion-icon
            ></ion-card-subtitle>
        </ion-card-header>

        <ion-card-content>
            {{ movie.overview }}
        </ion-card-content>
    </ion-card>
</template>

<script>
import {
    IonCard,
    IonCardContent,
    IonCardSubtitle,
    IonCardTitle,
    IonIcon,
} from "@ionic/vue";
import { star } from "ionicons/icons";
import { defineComponent } from "vue";

export default defineComponent({
    components: {
        IonCard,
        IonCardContent,
        IonCardSubtitle,
        IonCardTitle,
        IonIcon,
    },
    // Movie props from the parent component
    props: ["movie"],
    setup() {
        return { star };
    },
    data() {
        return {
            // Use a placeholder in case there is no poster
            url:
                this.movie.backdrop_path != null
                    ? "https://image.tmdb.org/t/p/original/" +
                      this.movie.backdrop_path
                    : "./assets/placeholder.jpg",
        };
    },
});
</script>
```

### Display Movie cards

Now we need to query the `TMDb` API to get the list of movies for each tab. We'll need axios for this so we'll install it before proceeding.

```
npm i axios
```

You can remove all styles from this section. Also we wont be using typescript here because I couldn't get it to work with infinite scrolling, which we'll be exploring later in this tutorial.

In `./src/Folder.vue`
```html
<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>{{ $route.params.id }}</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">{{ $route.params.id }}</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <div id="container">
        <!-- Loop over each movie -->
        <div v-for="movie in movies" :key="movie.id">
          <MovieCard v-bind:movie="movie"></MovieCard>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script>
// Remove typescript
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, 
IonTitle, IonToolbar } from '@ionic/vue';

import { ref } from "vue";
// Install Axios and import the Movie card component we just made
import MovieCard from "./MovieCard.vue";
import axios from "axios";

export default {
  name: 'Folder',
  components: {
    IonButtons,
    IonContent,
    IonHeader,
    IonMenuButton,
    IonPage,
    IonTitle,
    IonToolbar,
    // Add the MovieCard component
    MovieCard
  },
  data() {
    return {
      movies: ref([]),
      // Page to fetch
      pageNumber: 1,
      // Total number of pages present
      maxPages: 1,
      // Get the endpoint from the route parameter
      endpoint: this.$route.params.id
                .toLowerCase()
                .split(" ")
                .join("_"),
      // Which Country the user is in
      country: "",
    };
  },
  methods: {
    async fetch(pageNumber) {
      // Get Movies corresponding to which tab is open, Now playing, Upcoming, etc
      const movies = await axios.get(
          "https://api.themoviedb.org/3/movie/" +
              this.endpoint +
              "?api_key=<Your API KEY here>&language=en-US&page=" +
              pageNumber +
              "&region=" +
              this.country
      );
      // Populate movie list
      this.movies = movies.data.results;
      // Increase page counter by 1
      this.pageNumber = movies.data.page + 1;
      // Get total number of pages in response
      this.maxPages = movies.data.total_pages;
    }
  },
  mounted() {
    // Fetch movies when mounted
    this.fetch(this.pageNumber);
  },
  watch: {
    $route(to, from) {
      // Trigger when the route changes. i.e. when user switches tabs
      this.endpoint = this.$route.params.id
          .toLowerCase()
          .split(" ")
          .join("_");
      this.pageNumber = 1;
      this.maxPages = 1;

      // Fetch movies when route changes
      this.fetch(this.pageNumber);
    }
  }
}
</script>

<style scoped>
/* Remove styles */
</style>
```

After making these changes, you should get something that looks like this:

{{< figure link="/ionicvue/screenshot4.png" caption="Side menu" thumb="-thumb">}}

### Infinite scrolling

Now that we have our basic UI set up, we can focus on some quality of life improvements. When you query the `TMDb` api, you get a subset of the total movies in any category. This is because that's just one page. The movie lists are split into several pages, and that's good for us because it would take a long time to load such a huge amount of data at once.

However the user should have a way to see more content gradually until there is nothing else in the movie list. For this we'll set up infinite scrolling.

Basically once the user is about to reach the end of the content that has loaded so far, we'll send a request to the API for the next page, and add it to the list.

Luckily ionic has a component just for this.

In `./src/Folder.vue`.

```html
<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>{{ $route.params.id }}</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">{{ $route.params.id }}</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <div id="container">
        <div v-for="movie in movies" :key="movie.id">
          <MovieCard v-bind:movie="movie"></MovieCard>
        </div>
      </div>
      <!-- Add the infinite scroll component and call loadData -->
      <ion-infinite-scroll
          @ionInfinite="loadData($event)"
          threshold="100px"
          id="infinite-scroll"
          :disabled="isDisabled">
          <ion-infinite-scroll-content
              loading-spinner="bubbles"
              loading-text="Loading more movies...">
          </ion-infinite-scroll-content>
      </ion-infinite-scroll>
    </ion-content>
  </ion-page>
</template>

<script>
// Import the components
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, 
IonInfiniteScroll, IonInfiniteScrollContent, } from '@ionic/vue';

import { ref } from "vue";
import MovieCard from "./MovieCard.vue";
import axios from "axios";

export default {
  name: 'Folder',
  components: {
    IonButtons,
    IonContent,
    IonHeader,
    IonMenuButton,
    IonPage,
    IonTitle,
    IonToolbar,
    MovieCard,
    // Add the infinite scroll components
    IonInfiniteScroll,
    IonInfiniteScrollContent,
  },
  data() {
    return {
      movies: ref([]),
      pageNumber: 1,
      maxPages: 1,
      endpoint: this.$route.params.id
                .toLowerCase()
                .split(" ")
                .join("_"),
      country: "",
    };
  },
  methods: {
    async fetch(pageNumber) {
      const movies = await axios.get(
          "https://api.themoviedb.org/3/movie/" +
              this.endpoint +
              "?api_key=<Your API KEY here>&language=en-US&page=" +
              pageNumber +
              "&region=" +
              this.country
      );
      this.movies = movies.data.results;
      this.pageNumber = movies.data.page + 1;
      this.maxPages = movies.data.total_pages;
    },
    async pushData(pageNumber) {
      // Get the next page
      const movies = await axios.get(
          "https://api.themoviedb.org/3/movie/" +
              this.endpoint +
              "?api_key=<Your API KEY here>&language=en-US&page=" +
              pageNumber +
              "&region=" +
              this.country
      );
      // Add movies to current list
      this.movies = this.movies.concat(movies.data.results);
      this.pageNumber = movies.data.page + 1;
      this.maxPages = movies.data.total_pages;
    },
    async loadData(ev) {
      // Load the new data once we reach the end of the page
      const res = await this.pushData(this.pageNumber);
      console.log("Loaded data");
      console.log(res);
      ev.target.complete();

      // Once the last page has been fetched, we'll disable infinite loading
      if (this.pageNumber >= this.maxPages) {
          ev.target.disabled = true;
      }
    },
  },
  mounted() {
    this.fetch(this.pageNumber);
  },
  watch: {
    $route(to, from) {
      // Trigger when the route changes. i.e. when user switches tabs
      this.endpoint = this.$route.params.id
          .toLowerCase()
          .split(" ")
          .join("_");
      this.pageNumber = 1;
      this.maxPages = 1;

      // Fetch movies when route changes
      this.fetch(this.pageNumber);
    }
  }
}
</script>

<style scoped>
</style>
```

After you make these changes, you should see something like this

{{< figure link="/ionicvue/screenshot5.png" caption="Side menu" thumb="-thumb">}}

### Swipe down to reload

Another common feature in most mobile apps is the ability to refresh content when you swipe down at the top. This is useful because it's a simple intuitive gesture to get updated content.

And wouldn't you know it, Ionic has a component to help us with this too!

In `./src/Folder.vue`.
```html
<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>{{ $route.params.id }}</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <!-- Add refresher component -->
      <ion-refresher slot="fixed" @ionRefresh="doRefresh($event)">
        <ion-refresher-content></ion-refresher-content>
      </ion-refresher>
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">{{ $route.params.id }}</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <div id="container">
        <div v-for="movie in movies" :key="movie.id">
          <MovieCard v-bind:movie="movie"></MovieCard>
        </div>
      </div>
      <ion-infinite-scroll
          @ionInfinite="loadData($event)"
          threshold="100px"
          id="infinite-scroll"
          :disabled="isDisabled">
          <ion-infinite-scroll-content
              loading-spinner="bubbles"
              loading-text="Loading more movies...">
          </ion-infinite-scroll-content>
      </ion-infinite-scroll>
    </ion-content>
  </ion-page>
</template>

<script>
// Import the components
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, 
IonInfiniteScroll, IonInfiniteScrollContent, IonRefresher, IonRefresherContent, } from '@ionic/vue';

import { ref } from "vue";
import MovieCard from "./MovieCard.vue";
import axios from "axios";

export default {
  name: 'Folder',
  components: {
    IonButtons,
    IonContent,
    IonHeader,
    IonMenuButton,
    IonPage,
    IonTitle,
    IonToolbar,
    MovieCard,
    IonInfiniteScroll,
    IonInfiniteScrollContent,
    // Add the refresher components
    IonRefresher,
    IonRefresherContent,
  },
  data() {
    return {
      movies: ref([]),
      pageNumber: 1,
      maxPages: 1,
      endpoint: this.$route.params.id
                .toLowerCase()
                .split(" ")
                .join("_"),
      country: "",
    };
  },
  methods: {
    async fetch(pageNumber) {
      const movies = await axios.get(
          "https://api.themoviedb.org/3/movie/" +
              this.endpoint +
              "?api_key=<Your API KEY here>&language=en-US&page=" +
              pageNumber +
              "&region=" +
              this.country
      );
      this.movies = movies.data.results;
      this.pageNumber = movies.data.page + 1;
      this.maxPages = movies.data.total_pages;
    },
    async pushData(pageNumber) {
      const movies = await axios.get(
          "https://api.themoviedb.org/3/movie/" +
              this.endpoint +
              "?api_key=<Your API KEY here>&language=en-US&page=" +
              pageNumber +
              "&region=" +
              this.country
      );
      this.movies = this.movies.concat(movies.data.results);
      this.pageNumber = movies.data.page + 1;
      this.maxPages = movies.data.total_pages;
    },
    async loadData(ev) {
      const res = await this.pushData(this.pageNumber);
      console.log("Loaded data");
      console.log(res);
      console.log(this.pageNumber);
      ev.target.complete();
      if (this.pageNumber >= this.maxPages) {
          ev.target.disabled = true;
      }
    },
    async doRefresh(event) {
      // Get the movies from the first page again
      const res = await this.fetch(1);
      console.log(res);
      event.target.complete();
    },
  },
  mounted() {
    this.fetch(this.pageNumber);
  },
  watch: {
    $route(to, from) {
      this.endpoint = this.$route.params.id
          .toLowerCase()
          .split(" ")
          .join("_");
      this.pageNumber = 1;
      this.maxPages = 1;

      this.fetch(this.pageNumber);
    }
  }
}
</script>

<style scoped>
</style>
```

When you pull down from the top you should see something like the image below. When you release it, the content on the page should refresh.

{{< figure link="/ionicvue/screenshot6_LI.jpg" caption="Side menu" thumb="-thumb">}}

### Scroll to top when tab changes

If you're interacting with your app now, you might have noticed something. When you scroll down on one tab, Lets say `Popular` and then switch to another tab, say `Upcoming`, the scrollbar stays at the same position. This makes for a weird user experience, ideally we want it to auto scroll to the top whenever we switch tabs so that we can see the list of movies from the beginning instead of some random place on the page.


In `./src/Folder.vue`.
```html
<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>{{ $route.params.id }}</ion-title>
      </ion-toolbar>
    </ion-header>

    <!-- Enable scroll events and create a ref -->
    <ion-content :fullscreen="true" scrollEvents ref="content">
      <ion-refresher slot="fixed" @ionRefresh="doRefresh($event)">
        <ion-refresher-content></ion-refresher-content>
      </ion-refresher>
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">{{ $route.params.id }}</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <div id="container">
        <div v-for="movie in movies" :key="movie.id">
          <MovieCard v-bind:movie="movie"></MovieCard>
        </div>
      </div>
      <ion-infinite-scroll
          @ionInfinite="loadData($event)"
          threshold="100px"
          id="infinite-scroll"
          :disabled="isDisabled">
          <ion-infinite-scroll-content
              loading-spinner="bubbles"
              loading-text="Loading more movies...">
          </ion-infinite-scroll-content>
      </ion-infinite-scroll>
    </ion-content>
  </ion-page>
</template>

<script>
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, 
IonInfiniteScroll, IonInfiniteScrollContent, IonRefresher, IonRefresherContent, } from '@ionic/vue';

import { ref } from "vue";
import MovieCard from "./MovieCard.vue";
import axios from "axios";

export default {
  name: 'Folder',
  components: {
    IonButtons,
    IonContent,
    IonHeader,
    IonMenuButton,
    IonPage,
    IonTitle,
    IonToolbar,
    MovieCard,
    IonInfiniteScroll,
    IonInfiniteScrollContent,
    IonRefresher,
    IonRefresherContent,
  },
  data() {
    return {
      movies: ref([]),
      pageNumber: 1,
      maxPages: 1,
      endpoint: this.$route.params.id
                .toLowerCase()
                .split(" ")
                .join("_"),
      country: "",
    };
  },
  methods: {
    async fetch(pageNumber) {
      const movies = await axios.get(
          "https://api.themoviedb.org/3/movie/" +
              this.endpoint +
              "?api_key=<Your API KEY here>&language=en-US&page=" +
              pageNumber +
              "&region=" +
              this.country
      );
      this.movies = movies.data.results;
      this.pageNumber = movies.data.page + 1;
      this.maxPages = movies.data.total_pages;
    },
    async pushData(pageNumber) {
      const movies = await axios.get(
          "https://api.themoviedb.org/3/movie/" +
              this.endpoint +
              "?api_key=<Your API KEY here>&language=en-US&page=" +
              pageNumber +
              "&region=" +
              this.country
      );
      this.movies = this.movies.concat(movies.data.results);
      this.pageNumber = movies.data.page + 1;
      this.maxPages = movies.data.total_pages;
    },
    async loadData(ev) {
      const res = await this.pushData(this.pageNumber);
      console.log("Loaded data");
      console.log(res);
      console.log(this.pageNumber);
      ev.target.complete();
      if (this.pageNumber >= this.maxPages) {
          ev.target.disabled = true;
      }
    },
    async doRefresh(event) {
      const res = await this.fetch(1);
      console.log(res);
      event.target.complete();
    },
  },
  mounted() {
    this.fetch(this.pageNumber);
  },
  watch: {
    $route(to, from) {
      this.endpoint = this.$route.params.id
          .toLowerCase()
          .split(" ")
          .join("_");
      this.pageNumber = 1;
      this.maxPages = 1;

      this.fetch(this.pageNumber);
      // Scroll to top when the tab changes
      this.scrollToTop();
    }
  },
    setup() {
      // Get ref to content
      const content = ref();

      // Add function to scroll to top
      return {
        content,
        scrollToTop: () => content.value.$el.scrollToTop(),
      };
    },
}
</script>

<style scoped>
</style>
```



