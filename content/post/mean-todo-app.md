---
title: MEAN Todo app
subtitle: Creating website using the MEAN stack
date: 2018-08-09
#bigimg: [{src: "/img/wordSearch.png", desc: "Screenshot"}]
tags: [ "AngularJS" , "MongoDB" , "NodeJS" , "JavaScript" , "Express" , "Website" ]
---
 
If you want the code for this project, fork this GitHub repo - [`MEAN Todo app`](https://github.com/Aveek-Saha/MEAN-todo-app) <br>
If you want to see a live demoCheck it out here - [`Live demo`](https://todo0.herokuapp.com/)
 
<!--more-->
 
# Introduction
The MEAN stack, for those of you that haven’t heard of it, is a free JavaScript software stack for building modern web applications. This stack consists of – MongoDB,  Express, Angular, and NodeJS.  MongoDB is a NoSQL database that is highly scalable. Express is a middle-ware library that deals with routing in this case. AngularJS is a front end JavaScript library for creating UI. NodeJS is a JavaScript run-time environment that executes JavaScript code outside the browser, it allows us to run JavaScript on the server.
 
# CRUD Operations
At the heart of it all, what the app does is Create, Read, Update, and Delete documents in the database. The rest of the application simply provides an interface, both front end and back end to facilitate this. The Mongoose library has been used to interact with the MLabs MongoDB database.
 
Using Express we set up the routing for the CRUD operations, using the get method for Reading the list of existing Todos.<br>
{{< highlight javascript >}}
app.get('/api/todos', (req, res) =>{
    Todo.find((err, todos) => {
        if(err) res.send(err);
 
        res.json(todos);
    })
})
{{< / highlight >}}
 
The post method for Creating new Todos.<br>
{{< highlight javascript >}}
app.post('/api/todos', (req,res) =>{
    Todo.create({
        text: req.body.text,
        done: 'false'
    }, (err, todo) =>{
        if(err) res.send(err);
 
        Todo.find((err, todos) => {
        if(err) res.send(err);
 
        res.json(todos);
        });
    });
});
{{< / highlight >}}
 
The delete method for Deleting specific Todos. For the delete function we pass the ID of the todo to be deleted as a parameter.<br>
{{< highlight javascript >}}
app.delete('/api/todos/:todo_id', (req,res) =>{
    Todo.remove({
        _id: req.params.todo_id
    }, (err, todo) =>{
        if(err) res.send(err);
 
        Todo.find((err, todos) => {
        if(err) res.send(err);
 
        res.json(todos);
        });
    });
});
 
{{< / highlight >}}
 
On our front end, we have a counter that shows the number of tasks to do. This is found by simply counting the number of elements in the response by the get function. Below this is a list of todos, which have been fetched by querying the database for all documents in the todo collection. This is passed as an array to an Angular variable and we iterate through it to create the list of todos.
 
Each todo in this list has its own unique id, which is automatically created in mongodb when a new document is inserted. There is a delete button next to the text content of each task. On pressing this, the id of the todo is passed as a parameter to the delete method for Deleting specific Todos, and it updates the list.
 
There’s a form by which a user can enter a new task to do. On submitting this form, the text entered by the user is sent as a parameter to the route for the post method for Creating new Todos. This function in turn creates a new document in the database, and the list of todos is updated with the new task.
 
 
<br>