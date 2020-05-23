---
title: Getting started with Svelte and Electron
subtitle: A guide on using Svelte with electron to create desktop apps 
date: 2021-03-18
# bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "Svelte", "Desktop" , "Electron" ]
---


<!--more-->
 
# Introduction
This is a guide to get you up and running with Svelte + Electron. We'll just be going through the basic setup process to give you a starting point to develop desktop applications with Svelte by using Electron. If you want to learn more about Svelte basicks you can check out this tutorial here: []().
 
You can follow this tutorial even if you're not too familiar with Svelte because we'll just be setting things up for the project here.

# Create a Svelte app
To get started we'll first be cloning the Svelte boilerplate using a tool called degit. Lets start by installing `degit` using

```bash
npm install -g degit
```

Then we install the boilerplate and install all the dependencies.

```bash
npx degit sveltejs/template svelte_electron
cd svelte_electron
npm install
```

To run the dev server run
```bash
npm run dev
```

Then open http://localhost:5000, you should see a defalut web page. This means that the setup was successful

# Set up Electron
Now we'll install electron as a development dependency

```
npm i -D electron@latest
```

Now we need to set up some scripts in our `package.json`
```json


```

<!-- {{< highlight javascript >}}
{{< / highlight >}} -->
<br>


