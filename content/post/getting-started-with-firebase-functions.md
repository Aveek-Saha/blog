---
title: Getting started with Firebase Functions
subtitle: A beginner's tutorial on creating and deploying a Firebase function
date: 2020-03-12
# bigimg: [{src: "/svelte/svelte.svg", desc: "Svelte"}]
tags: [ "Firebase", "Cloud", "Function", "Javascript" ]
---
 
Check the final application here - [`pix2ascii`](pix2ascii.web.app).
 
The full code for this project can be found on [`GitHub`](https://github.com/Aveek-Saha/pix2ascii) 
<!-- or read the next part here: [`Deploying Svelte apps to Firebase with GitHub actions`]() -->
 
<!--more-->
 
# What are Firebase functions?
 
From the [documentation](https://firebase.google.com/docs/functions):
> Cloud Functions for Firebase is a serverless framework that lets you automatically run backend code in response to events triggered by Firebase features and HTTPS requests.
 
What this means is essentially you can write code that will run when certain events take place. These events can be http requests or an event triggered by another firebase service, like their database or storage solution. This means that you can essentially write server side code without having to worry about the specifics of managing and scaling servers, which makes life a lot easier as a developer.
 
Not all servers can be replaced by cloud functions especially since only Javascript or Typescript is supported. However there are a lot of simple tasks that you might have been considering using a server for, that can be handled by a cloud function.
 
To demonstrate how to use Firebase cloud functions we'll be creating a simple application. In this application you can upload an image which will be converted to [ascii art](). Probably not the most useful application in the world, but a fun project to help you get started with firebase functions. The backend of this application will only use Firebase cloud functions and for the frontend we'll be using [Svelte](). 
 
I'll be tackling the Frontend in another post so stay tuned for that. So in this tutorial you'll learn how to create and deploy a Firebase function.
 

# Set up Firebase
Before we start you'll need an account to log in to the [`Firebase Console`](https://console.firebase.google.com/), and then you can follow the steps below.
 
### 1. Create a Firebase project- 
Go to the firebase console and create a new project. You can choose to set up Google analytics for the project, but it's not really important for this application.
 
### 2. Set up Firebase CLI- 
You'll need to have Node.js installed already on your system. Install the cli through npm by running `npm install -g firebase-tools`
 
### 3. Initialize the Firebase SDK-
Run `firebase login` to log in to the CLI via the browser using the account your new project is linked to.
 
### 4. Initialze the project-
Create a new working directory for this project and navigate to that directory. Then run `firebase init functions`.
 
During the setup, Use an existing project, and select the project you'd created through the console in the step before. Select JavaScript as the language and pick install dependencies using npm when it offers you the option. Once all the dependencies have finished isntalling you're ready for the next step!
 
# Creating the Firebase function
 
If the setup went correctly you should be seeing a folder called `functions`. This is where the code for our function will be, specifically in `index.js`.
 
## The API
 
We'll be setting up a basic Express server with only one endpoint that receives the uploaded image and then using this Express app as a cloud function.
First install the required modules
```bash
npm i
npm i express busboy
```
 
If you want to test the function locally, run `firebase serve` and then use the url specified in the console to test the function.
 
We'll be uploading an image using a multipart form. Unfortunately common middleware for this purpose like Multer and Formidable dont work properly with express in Cloud functions, so we'll have to use a method shown in the [documentation](https://cloud.google.com/functions/docs/writing/http#multipart_data) that uses Busboy. The documentation does a pretty good job of explaining the code, so I'll just be copying it here in `index.js`.
 
{{< highlight javascript >}}
const functions = require('firebase-functions');
const express = require('express');
const cors = require('cors');
const Busboy = require('busboy');
const path = require('path');
const os = require('os');
const fs = require('fs');
 
const app = express();
 
const runtimeOpts = {
    timeoutSeconds: 120
}
 
// Automatically allow cross-origin requests
app.use(cors({ origin: true }));
 
app.post('/', (req, res) => {
 
    const busboy = new Busboy({ headers: req.headers });
    const tmpdir = os.tmpdir();
    const fields = {};
    const uploads = {};
 
    busboy.on('field', (fieldname, val) => {
        console.log(`Processed field ${fieldname}: ${val}.`);
        fields[fieldname] = val;
    });
 
    const fileWrites = [];
 
    // This code will process each file uploaded.
    busboy.on('file', (fieldname, file, filename) => {
        console.log(`Processed file ${filename}`);
        const filepath = path.join(tmpdir, filename);
        uploads[fieldname] = filepath;
 
        const writeStream = fs.createWriteStream(filepath);
        file.pipe(writeStream);
 
        const promise = new Promise((resolve, reject) => {
            file.on('end', () => {
                writeStream.end();
            });
            writeStream.on('finish', resolve);
            writeStream.on('error', reject);
        });
        fileWrites.push(promise);
    });
 
    busboy.on('finish', async () => {
        await Promise.all(fileWrites);
        for (const file in uploads) {
            console.log(uploads[file]);
            
            fs.unlinkSync(uploads[file]);
 
        }
        res.send();
    });
 
    busboy.end(req.rawBody);
 
});
 
// Expose Express API as a single Cloud Function:
exports.ascii = functions.runWith(runtimeOpts).https.onRequest(app);
{{< / highlight >}}
 
So now if we test the function using a service like Insomnia or Postman by uploading an image in a multi part form the path to the image uploaded should be logged in the console!
 
## Image to ASCII
 
We'll be creating a separate module for the logic to convert the image the user uploads to ASCII art. To convert the image we'll be using a module called `Jimp`.
```bash
$ npm i jimp
```
 
Create a new file called `img2ascii.js`.
 
{{< highlight javascript >}}
 
const Jimp = require('jimp');
 
// Export the function so we can call it from the cloud function
// The function takes the filepath, the dimensions of the image 
// and the kind of ascii art as parameters
 
exports.convert = function (file, dim, options) {
 
    // Different kinds of character sets for visually different ends results
    var greyscale = {
        gscale_70: "@$B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\\|()1{}[]?-_+~<>i!lI;:,\" ^`'. "
        .split("").reverse().join(""),
        gscale_10: "@%#*+=-:. ".split("").reverse().join(""),
        gscale_block: "  ░▒▓█"
    }
 
    var gscale = greyscale[options]
 
    var norm_factor = (255 * 4) / gscale.length
 
    // Jimp.read returns a promise, so we'll pass that on
    return Jimp.read(file).then(
        (image) => {
            // Since the letters are almost twice as tall as they are wide, 
            // we'll be scaling the height and then dividing by 2 so that the
            // result isn't abnormally tall and proportions are preserved
            var height = Math.round((image.bitmap.height * width) / (2 * image.bitmap.width))
            
            image
                .resize(width, height) // resize
                .greyscale() // set greyscale
 
            var arr = []
 
            image.scan(0, 0, image.bitmap.width, image.bitmap.height, 
            function (x, y, idx) {
 
                var red = this.bitmap.data[idx + 0];
                var green = this.bitmap.data[idx + 1];
                var blue = this.bitmap.data[idx + 2];
                var alpha = this.bitmap.data[idx + 3];
 
                var rgba = red + green + blue + alpha
                var intensity = Math.round(rgba / norm_factor) - 1
 
                // Map intensity to a character
                arr.push(gscale[intensity])
            });
 
            var matrix = [];
            // Reshape the array
            while (arr.length) matrix.push(arr.splice(0, dim.width));
 
            var toWrite = ""
 
            // Convert the array to a string
            matrix.forEach(element => {
                toWrite += element.join("") + '\n'
            });
 
            return toWrite
 
        }
    ) 
}
 
{{< / highlight >}}
 
Now to convert the image the user has passed to the function, we have to make a few changes to `index.js`.
 
Import the module we just created.
 
{{< highlight javascript >}}
...
const p2a = require('./img2ascii.js')
...
{{< / highlight >}}
 
 
To take the file passed and do the conversion, modify this part of the cloud function
{{< highlight javascript >}}
...
...
busboy.on('finish', async () => {
        await Promise.all(fileWrites);
        var art = []
 
        for (const file in uploads) {
            // Call the conversion function on the file that's been passed along with the other parameters
            var ascii = await p2a.convert(uploads[file], parseInt(fields['width']), fields['charset'])
            // Store the result in an array
            art.push(ascii)
            fs.unlinkSync(uploads[file]);
 
        }
        // Since we're only allowing one file to be uploaded
        res.send(art[0]);
    });
...
...
{{< / highlight >}}
 
At this stage if you test your function using Insomnia or Postman to create a multi part form, with an image, the width and the charset fields, and send it, you should see a string of ascii characters that represent your image being sent back in the response. We're now ready for the next step!
 
# Deploy
To deploy the function just run this command in the `functions` folder.
```bash
firebase deploy
```
Once the application has been deployed, the CLI will give you a public url that you can access from anywhere. It should look something like this. `https://us-central1-pix2ascii.cloudfunctions.net/ascii`. You can test this deployed function in the same way you were testing it localy, just replace the url.
 
 
# Conclusion
We've now successfully created and deployed a function to firebase that we can use to convert an image to ASCII art. If all you want is the backend then that's all you need.
 
You're free to use any hosting service along with any framework of your choice for the web UI. If you want to also learn how to build and deploy a svelte application to Firebase and automate the process using GitHub actions, be sure to look out for the next part of this tutorial. 
<!-- follow this next tutorial [`Deploying Svelte apps to Firebase with GitHub actions`]() -->
 
 
 
<!-- {{< highlight javascript >}}
{{< / highlight >}} -->
<br>