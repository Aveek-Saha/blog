---
title: Getting started with Svelte and Electron
subtitle: A guide on setting up Svelte with electron to create desktop apps 
date: 2021-03-18
# bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "Svelte", "Desktop" , "Electron" ]
---

The code for this tutorial can be found [here](https://gitlab.com/aveeksaha/electron-svelte)

<!--more-->
 
# Introduction
This is a guide to get you up and running with Svelte + Electron. We'll just be going through the basic setup process to give you a starting point to develop desktop applications with Svelte by using Electron. If you want to learn more about Svelte basicks you can check out this tutorial here: [Get started with Svelte](https://home.aveek.io/blog/post/get-started-with-svelte/).
 
You can follow this tutorial even if you're not too familiar with Svelte because we'll just be setting things up for the project here.

# Create a Svelte app
To get started we'll first be cloning the Svelte boilerplate using a tool called degit. Let's start by installing `degit` using

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

Then open http://localhost:5000, you should see a default web page. This means that the setup was successful

# Set up Electron
Now we'll install `electron` as a development dependency.

```
npm i -D electron@latest
```

Then we'll install `electron-reload` and `electron-is-dev` for some quality of life improvement during development. `electron-reload` will let us watch files and reload the electron app automatically to reflect changes we make to the code. `electron-is-dev` will allow us to detect if the application is running in production or development.

```
npm i -D electron-reload electron-is-dev
```

Now we need to create an entry point for our electron app. Create a file called `main.js` with the following code.

```javascript
const { app, BrowserWindow } = require('electron')
const path = require('path')

const isDev = require('electron-is-dev');

// If in development use electron-reload to watch for
// changes in the current directory
if (isDev) {
    require('electron-reload')(__dirname, {
        electron: require(`${__dirname}/node_modules/electron`)
    });
}

function createWindow() {

    // Create the browser window with node integration
    const win = new BrowserWindow({
        width: 800,
        height: 600,
        webPreferences: {
            nodeIntegration: true
        }
    })

    win.loadURL(
        url.format({
            pathname: path.join(__dirname, 'public/index.html'),
            protocol: 'file:',
            slashes: true
        })
    )

    // Open the DevTools only if app is in development
    // If in production, don't show.
    if (isDev)
        win.webContents.openDevTools()
}

app.whenReady().then(() => {
    createWindow()

    app.on('activate', function () {
        if (BrowserWindow.getAllWindows().length === 0) createWindow()
    })
})

app.on('window-all-closed', function () {
    if (process.platform !== 'darwin') app.quit()
})

```

Now we need to set up some scripts in our `package.json` and define the electron entry point.
```json
{   ....
    "main": "main.js",
    "scripts": {
        ...
        "app": "electron ."
        ...
    }
    ....
}
```

There's one last step before your application can be rendered. In `public/index.html` we need to make the urls absolute so that Electron can find them.

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset='utf-8'>
	<meta name='viewport' content='width=device-width,initial-scale=1'>

	<title>Svelte app</title>

	<link rel='icon' type='image/png' href='favicon.png'>
	<link rel='stylesheet' href='global.css'>
	<link rel='stylesheet' href='build/bundle.css'>

	<script defer src='build/bundle.js'></script>
</head>

<body>
</body>
</html>
```
Now run `npm run build` to create the compiled JavaScript and CSS files in the `public/build/` directory.

Now if you run `npm run app` you should see the same default welcome page you saw on your browser in an electron window.

# Live reload both electron and Svelte
The way the application is set up now, You could run `npm run dev` in one terminal to enable live reload for Svelte, and `npm run app` in another terminal to live reload the electron app. This is a little inconvenient and we can combine both these functions and run them simultaneously.

For this we'll be using `concurrently`. So we'll first install it and then update our scripts in `package.json`.

```
npm i -D concurrently
```

Then we'll create a script to run both Svelte and Electron parallelly.

```json
{
    "scripts": {
        ...
        "app-dev": "concurrently \"npm:dev\" \"npm:app\""
        ...
  },
}
```

By running `npm run app-dev` you can live reload both Electron and Svelte!

# Conclusion
Now you have a starting point to start building your Electron apps with Svelte at the frontend. While there are some starter templates available, the setup process for this is pretty straightforward and easy to do by yourself.

If you're interested in learning Svelte you can check out this tutorial here: [Get started with Svelte](https://home.aveek.io/blog/post/get-started-with-svelte/).

<!-- {{< highlight javascript >}}
{{< / highlight >}} -->
<br>


