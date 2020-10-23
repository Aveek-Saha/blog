---
title: Getting started with Vuejs and the GitHub API
subtitle: A beginners tutorial on how to make a web app with Vue.js
date: 2018-11-10
# bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "Vue" , "Vuejs" , "GitHub" , "Website" ]
---

If you want the code for this project, fork this GitHub repo - [`GitHub Stats`](https://github.com/Aveek-Saha/GithubStats) <br>
If you want to see a live demo, try it out here - [`Live Demo`](https://githubstats0.firebaseapp.com/)

<!--more-->

# Introduction
This tutorial is for beginners to Vue.js, which is a progressive JavaScript framework like Angular and React. You can read more about vue [`here`](https://vuejs.org/). Since Vue is a JavaScript framework, you'll need basic knowledge of HTML, JS and CSS to follow along.

I'll be teaching you how to make a web app where you can check the download counts of GitHub releases, by querying the [`GitHub API`](https://developer.github.com/v3/).

### What are releases?
Releases are GitHub's way of packaging and providing software to users. Repository owners can upload downloadable files that users can install or extract on to their systems as a way of distributing software. An example can be seen here [`Music Player releases`](https://github.com/Aveek-Saha/MusicPlayer/releases).

There is one issue with this system though, it doesn't display the number of times your software or packages were downloaded. It is very likely that someone who released software would want to know how popular it is, or how many people are using his software. So we're going to build an application that helps us fill this void.

# Setup
There are many ways to include Vue.js in an application, but for this tutorial we will be using the Vue CLI.

Install the CLI.

{{< highlight javascript >}}
npm install -g @vue/cli
{{< /highlight >}}


To create a new project, run:
{{< highlight javascript >}}
vue create vue-tut
{{< / highlight >}}
You'll get a prompt to choose a ```default``` preset or ```manually select features```. The default option will be fine for us, so just press enter and wait for the CLI to initialize the project. Cd into the directory and run `npm run serve`. If the project was created properly, you should see a default web page when you go to `http://localhost:8080`.

The project folder will have a structure like this
{{< highlight javascript >}}

vue-tut
  |----node_modules
  |
  |----public
  |     |--index.html
  |     |--favicon.ico
  |
  |----src
  |     |----assets // logo.png
  |     |----components // contains a HelloWorld.vue component
  |     |
  |     |--App.vue // main component
  |     |--main.js
  |
  |-- // other files...
  |
  |--package.json

{{< / highlight >}}


Some useful Scripts:

* Run on localhost: `npm run serve`
* Build for production: `npm run build`

We'll be working with mostly the files in the ```src``` folder. In ```index.html``` we're going to add Bootstrap and JQuery to give our app some visual appeal. Put the following in the head tag.

{{< highlight html >}}
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo"
crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49"
crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy"
crossorigin="anonymous"></script>
{{< / highlight >}}

We will get the download numbers of each file in the releases through the GitHub API. To make these Api requests we're going to use ```Axios```, which is a promise based HTTP client for the browser and node.js. To add axios run `npm i axios`.

# Components
Vue components have 3 parts

{{< highlight html >}}

<template>
   <!-- for HTML and components  -->
</template>

<script>
   // data and methods of the component
</script>

<style>
   /* styles for the component */
</style>

{{< / highlight >}}

Let's start off by renaming ```HellWorld.vue``` to something more apt, like ```Data.vue```, since we're going to process and display the API request data there. After renaming be sure to rename all the occurrences of ```HellWorld``` with ```Data```, in both ```Data.vue``` and ```App.vue```.

In ```Data.vue``` remove everything in between the ```style``` tag and the ```template``` tag. Your file should look like this.

{{< highlight html >}}

<template>
</template>

<script>
export default {
  name: 'Data',
}
</script>

<style scoped>

</style>

{{< / highlight >}}

We're going to add a navbar, a container div and some styles to ```App.vue``` and file should look like this.
{{< highlight html >}}

<template>
 <div id="app">
 <!-- added a navbar -->
   <nav class="navbar navbar-dark bg-dark">
     <span class="navbar-brand mb-0 h2">GitHub Download Stats</span>
     <a href="https://aveek-saha.github.io/" class="navbar-brand mb-0 h1 text-right">
       <i class="fab fa-github fa-inverse"></i>
     </a>
   </nav>

   <!-- wrapped the data component in a container -->
   <div class="container">
   <!-- inject the Data component -->
     <Data/>
   </div>

 </div>
</template>

<script>
   // import the data component
import Data from './components/Data.vue'

export default {
    name: 'app',
    components: {
        Data
    }
}
</script>

<style>
   /* global styles */
body{
    background-color: #FEFEFE
}

.asset{
    word-break: break-all;
}

#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
}
</style>

{{< / highlight >}}
This is basically all the modification we'll be doing to ```App.vue```. All the magic happens in ```Data.vue```, where our main logic resides.

### Data.vue
This is where we'll be making the API requests and in order to use ```axios``` it has to imported to the component. Along with this, we'll also be creating some variables in the ```script``` section of this component.

{{< highlight javascript >}}
// import axios
import axios from 'axios';

export default {
 name: 'Data',
 // all the data belonging to this component goes
 // to data() and is returned as an object
 data() {
   return {
       // declare some variables that we'll use later
      
       // owner of the repository
       username: "",
       // name of the repo
       repository: "",
       // list of releases
       releases: [],
       // total downloads of all releases combined
       grandTotal: 0,
       // if the query returned a status 404 or not
       success: true,
       // does the repository have any releases?
       empty: false,
       // base url
       url: "https://api.github.com"
   }
 }
}
{{< / highlight >}}

Now we need to make a form so that people can enter ```username``` and ```repository```, we'll add this in the ```template``` tag.

{{< highlight html >}}

<div class="row justify-content-center">
   <div class="col-lg-6">

       <div class="card bg-light shadow-sm mb-3 rounded" style="margin-top: 2%">

           <div class="card-body">
               <h5 class="card-title">Enter Details</h5>

               <form>
                   <div class="form-group">
                       <input type="text" class="form-control form-control-sm" v-model="username" id="username" placeholder="Your username" >
                   </div>

                   <div class="form-group">
                       <input type="text" class="form-control form-control-sm" v-model="repository"
                       list="repos" id="repository" placeholder="Repository name" v-on:keyup.enter="sendReq">
                   </div>
               </form>

               <button class="btn btn-outline-dark" v-on:click="sendReq">Submit</button>
           </div>
       </div>

   </div>
</div>

{{< / highlight >}}

Shown in the code above are two important concepts in Vue:

- **Data Binding-**
In this line below we use `v-model="username"` this means that we're binding the value of the input to the variable `username`. As the value of the input field changes, so does the variable `username` in `data()` and vice versa.
{{< highlight html >}}
<input type="text" class="form-control form-control-sm"
v-model="username" id="username" placeholder="Your username" >
{{</ highlight >}}


- **Event Handling-**
Here we use `v-on:click="sendReq"` which means that when we click on this button it will call `sendReq` which is a method we will be implimenting now.
{{< highlight html >}}

<button class="btn btn-outline-dark" v-on:click="sendReq">Submit</button>
{{</ highlight >}}


So now we have the input form, that does nothing when we click Submit as of now. We need to write the `sendReq` method which is going to send a request to the GitHub api. Make the following changes in ```script```

{{< highlight javascript >}}
import axios from 'axios';

export default {
 name: 'Data',
 data() {
   return {
     username: "",
     repository: "",
     releases: [],
     grandTotal: 0,
     success: true,
     empty: false,
     url: "https://api.github.com"
   }
 },
 // add methods
 methods: {

   sendReq: function(){
     var that = this;
     this.empty = false;

     // get data using a promise with axios
     // the request url has the format base + /repos/:user/:repo/releases
     axios.get(this.url + '/repos/' + this.username + "/" + this.repository + "/releases" )
       .then(function (response) {

         var data = response.data

         // the total of all the release downloads
         that.grandTotal = 0

         for (let i = 0; i < data.length; i++) {

           // total of a particular release version
           var total = 0
           data[i].total = 0

           for (let j = 0; j < data[i].assets.length; j++) {
             // add up the download counts of the individual
             // binary/packaged files for a particular release version
             total += parseInt(data[i].assets[j].download_count)
           }
           // add a new field to the data object
           data[i].total = total
           that.grandTotal += total
         }

         // that.releases is an array of releases
         that.releases = data

         // if we got this far that means the request was a success
         that.success = true
         if(response.data.length === 0){
             // check if there are any releases for the repo
           that.empty = true
         }       
       })
       .catch(function (error) {
           // if there's an error then the request was not a success
         that.success = false
         that.empty = false

       });
      
   }
 }
}
{{< / highlight >}}

So now if we enter a valid ```username``` and ```repository``` and click Submit, the response will be stored in `releases`. Now we need a way to display the information that we got. Since `releases` is an array, we'll use `v-for` to iteratively display one card for each release.

For this, we're going to create a new component called `Card`, and we'll pass data from the `Data` component via **props** (Properties). This means that whatever value we pass to a prop from parent can be read by the child component and becomes a property of that component.

Add the following lines to `Data.vue`

In `Template`
{{< highlight html >}}
<div class="row justify-content-center">
   <div class="col-lg-6">

       <h5 v-if="grandTotal && success" style="margin-bottom: 3%;">
           Total Downloads: {{grandTotal.toLocaleString()}}
       </h5>

       <!-- show a card for every element in releases -->
       <!-- release and index are props in the <Card> component -->
       <Card v-for="(release , index) in releases" :key="index" v-bind:release="release" v-bind:index="index" ></Card>

       <!-- if request not successful then say that the repo could not be found -->
       <h5 v-if="!success" style="margin: 2%;">
           No repository found
       </h5>

       <!-- if the repo exists but it has no releases, show that it has none -->
       <h5 v-if="empty" style="margin: 2%;">
           No Releases
       </h5>

   </div>
</div>
{{< / highlight >}}

In `script` add
{{< highlight javascript >}}
// card component that we'll be creating
import Card from './Card.vue'

export default {
  name: 'Data',
  components: {
    Card
  },
  // ... rest of the exports
}

{{< / highlight >}}

Here we get to see 3 more important features of Vue:

- **List Rendering-**
The `v-for` directive is used to render a list of items based on an array. Another way to use the `v-for` directive is `v-for="item in items"`, which is simpler. In this case we render cards based on the values of the the `releases` array.
{{< highlight html >}}
<div class="card shadow-sm mb-3 rounded"
v-for="(release , index) in releases" :key="index">
{{</ highlight >}}

- **Conditional Rendering-**
We use `v-if` to show or hide content based on conditions. For example in this case, we want to show this message only when the GET request was not successful. `v-if` can be used with `v-else` and `v-else-if`.
{{< highlight html >}}

<h5 v-if="!success" style="margin: 2%;">
   No repository found
</h5>
{{</ highlight >}}

- **Class and style binding-**
The `v-bind:class` or `v-bind:style` can be used to manipulate the class or style of an element based on a condition. As you'll see below, if `release.prerelease` is true then the div will be given a class of `alert-warning`, and if `index == 0` i.e. it's the first element in the array, then the div will be given a class of `alert-success`.
{{< highlight html >}}

<div class="card-header"
v-bind:class="{ 'alert-warning': release.prerelease , 'alert-success': index == 0 }" >
{{</ highlight >}}


### Card.vue
Now we'll be creating the card component. Create a file called `Card.vue` and put the following in the file.

{{< highlight html >}}

<template>
    <div class="card shadow-sm mb-3 rounded " style="margin-top: 2%; text-align:left" v-if="success" v-for="(release , index) in releases" :key="index">

           <div class="card-header" v-bind:class="{ 'alert-warning': release.prerelease , 'alert-success': index == 0 }" >
               <h5 class="card-title" style="margin-bottom:0px">

               <!-- Use the prop values passed by the parent -->
                   <a v-bind:href="release.html_url" class="alert-link" style="color:inherit"> {{release.tag_name}}</a> &nbsp;> &nbsp;
                   <span class="text-muted h6">
                   {{release.total.toLocaleString()}} downloads
                   </span>
               </h5>
           </div>

           <div class="card-body" >

           <!-- create a table with the name of the file and the number of downloads -->
               <table class="table table-borderless table-sm">
                   <thead>
                       <tr>
                           <th scope="col">Name</th>
                           <th scope="col">Downloads</th>
                       </tr>
                   </thead>
                   <tbody>
                       <tr v-for="( asset , index) in release.assets" :key="index">
                           <th scope="row" class="asset">{{asset.name}}</th>
                           <td>{{asset.download_count.toLocaleString()}} </td>
                       </tr>
                   </tbody>
               </table>

           </div>
       </div>
</template>

<script>

export default {
  name: 'Card',
  // Props that become properties when release and index are passed from the parent
  props: ['release', 'index'],
}
</script>

<style scoped>

.alert-warning{
  color: inherit
}
.alert-success{
  color: inherit
}
</style>

{{< / highlight >}}


# Wrapping it up
Now if we serve the application with `npm run serve`, you should see something like this:

![Screenshot1](/githubstats/screenshot2.png)

When you click submit, or enter after typing a valid repository with releases, you should see the total downloads and the downloads for each individual release:

![Screenshot2](/githubstats/screenshot3.png)

That's it! You've made a real world application using Vue from scratch and used some important features like-

- **Data Binding**
- **Event Handling**
- **List Rendering**
- **Conditional Rendering**
- **Class and style binding**


A slightly modified version of this project can be found on my GitHub - [`GitHub Stats`](https://github.com/Aveek-Saha/GithubStats)

And a live version of the code above can be found here - [`Live Demo`](https://githubstats0.firebaseapp.com/)


<!-- {{< highlight javascript >}}
{{< / highlight >}} -->
<br>