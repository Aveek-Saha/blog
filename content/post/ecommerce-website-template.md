---
title: E-commerce Website Template
subtitle: A template
date: 2018-07-22
#bigimg: [{src: "/img/wordSearch.png", desc: "Screenshot"}]
tags: [ "JavaScript" , "PHP" , "MySQL" , "Website" ]
---

If you want the code for this project, fork this GitHub repo - [`E-commerce template`](https://github.com/Aveek-Saha/ecommerce-website-template) <br>
If you want to see a live demo, check this out - [`Demo`](http://eshop.byethost22.com/)

<!--more-->

# Introduction
A common thing that most of us on the internet have done, is shop online. The idea of spending your hard earned money online is as addictive and dopamine inducing to those that enjoy the same thing in a mall.  This project is an attempt to create a template for a simple e-commerce site, without any payment capability.

On loading the site, you can either login with an existing account or create a new one. Account details are stored in a SQL database, and passwords are hashed for security.

# Logging in as a User
Once logged in, you’ll be able to see all product listings along with the price of the product and a button to add the product to your cart.  If you click on a product, It’ll take you to a Product detail page, displaying more information like a full product description and a list of dealers.

On the nav bar, you can check how many items are in your cart, clicking on it will take you to a page with the breakdown of the current bill, you can remove items from here by deleting them, or you can change the quantity of any item in the cart.  This page Displays your account details like, Name, address and phone number. you can proceed to checkout from here and place your order, this order is stored in an SQL database. Currently there is no way for a  user to view the orders he’s already placed.

There is also a messaging facility available, and you can enter the email of the user you want to message, and send messages back and forth from that user.

# Logging in as an Admin
If you are logged in as an admin, you will have the option to create new products, and add their details.

<br>