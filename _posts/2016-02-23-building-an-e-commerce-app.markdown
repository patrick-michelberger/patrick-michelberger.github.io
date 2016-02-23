---
layout: post
title:  "Building An E-Commerce App With Node.js & The ABOUT YOU API"
date:   2016-02-23 12:00:00
categories: tutorial
---

ABOUT YOU is a new fashion online shop with more than 55.000 products and offers an outstanding developer platform to create your own E-commerce app.
You write the code to build the showcase, ABOUT YOU manages the whole backend, payment & fulfillment process.

<img src="https://cdn-images-1.medium.com/max/1600/1*oqu-8sEwvDuqQpTeYco5Kw.png" width="100%" />

This is going to be a tutorial which consists of three parts:
Initial setup, using the ABOUT YOU SDK for Node.js and creating an UI with a shopping basket.

### Part I: Initial setup

For the first part of this tutorial I show you the inital setup & required packages to build our Node.js web app.

#### Packages

Our application uses the Express framework which can be considered the de facto standard for Node.js development.
I assume that you’ve got the node & npm executables setup already.

#### Installing Express

To install Express globally use the following command

~~~ 
npm install -g express
~~~ 

After that we’re going to create a new folder and use Express to setup a default Node.js project

~~~ 
mkdir testapp
cd testapp
express --hbs
npm install
~~~

app.js is basically the skeleton of your application and is mainly responsible for bootstrapping your application.
package.json contains all requirements and should look something like this:

~~~
{
  "name": "testapp",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "express": "~4.9.0",
    "body-parser": "~1.8.1",
    "cookie-parser": "~1.3.3",
    "morgan": “~1.3.0",
    "serve-favicon": "~2.1.3",
    "debug": "~2.0.0",
    "hbs": "~2.7.0"
  }
}
~~~

Next to Express we defined handlebars for rendering our server-side templates. In your app.js we can get rid of the following lines.

~~~ 
var users = require(‘./routes/users’);
app.use('/users', users);
~~~ 

Also delete /routes/users.js as we are not going to need it for the project.
In this tutorial most of the implementation is going to happen in routes/index.js. This is the place where we define the functions to be executed when our application receives HTTP requests.
You can run the project by executing

~~~ 
npm start
~~~ 

in your project’s root folder and navigate to http://localhost:3000. Please note, npm start is an alias for node ./bin/www defined in the package.json file.

<img src="https://cdn-images-1.medium.com/max/2000/1*5Nr4CbBUkZQXfCOf9hItBA.png" width="100%"/>

Congratulations, we have finished all the required setup and we can now concentrate on the ABOUT YOU SDK integration in the following part of this tutorial.