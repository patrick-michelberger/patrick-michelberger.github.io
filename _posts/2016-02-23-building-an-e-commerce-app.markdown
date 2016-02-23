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

## Part I: Initial setup

For the first part of this tutorial I show you the inital setup & required packages to build our Node.js web app.

### Packages

Our application uses the Express framework which can be considered the de facto standard for Node.js development.
I assume that you’ve got the node & npm executables setup already.

### Installing Express

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

Congratulations, we have finished all the required setup and we can now concentrate on the ABOUT YOU SDK integration. 

## Part II: ABOUT YOU SDK

We’re going to setup the two needed API calls to fetch you app’s categories and to execute an arbitrary product search in the following section.

### Packages

To get going we install the SDK via npm

~~~
npm install aboutyou-sdk --save
~~~

Using the option —save automatically adds the module to the dependency section of our package.json file

~~~
“aboutyou-sdk”: “^0.1.0"
~~~

Your version is probably newer than 0.1.0 — it’s just the current one available right now.

### Credentials

The use of the ABOUT YOU API requires credentials that you obtain on developer.aboutyou.de
To do so, create a new application and take a look on the overview page. There you can find your App-ID and credentials (token & secret).

<img src="https://d262ilb51hltx0.cloudfront.net/max/2000/1*PzhFtSAXlNo68a-bGfSZgQ.png" width="100%"/>

For the sake of convenience we’re going to use the test credentials that allow us to access a sample’s app category & product catalogue.

### Setting up the configuration

We need to add the ABOUT YOU SDK to our app.js file and initialize it with our sample App-ID and token. For the sake of convenience, we intercept each http request and bind the ay object to the req parameter.

~~~
var aboutYou = 
require('aboutyou-sdk')(100,'3ed93394c2b5ebd12c104b177b928ad0');
// Make the AboutYou module accessible to our router
app.use(function(req,res,next){ 
  req.aboutYou = aboutYou;
  next();
});
~~~

Having made these changes the SDK is now accessible to all of our app’s routes and we’re finally ready to start our categories & products integration.

### Fetching your app’s category tree
A category is a simple JSON object that looks like this

~~~
{
  "id": 74415,
  "name": "Frauen",
  "active": true,
  "subcategories": [{
    "id": 74417,
    "name": 'Shirts',
    "active": true,
    "subcategories": []
  }]
}
~~~

Categories are basically a defined product filter. You use them to decide which of the more than 50.000 ABOUT YOU products should be offered in your app. Lastly, they are also used as a navigation tool through your app.
You can create & configure them in the Developer Center:

<img src="https://d262ilb51hltx0.cloudfront.net/max/2000/1*eGXosh7rf5PP1Yrh4AiL-w.png" width="100%" />

Once you finished your categories settings you can query them through our SDK method fetchCategoryTree().
To make your category tree accessible via an API call we’ll extend index.js with a /api/categories route:

~~~
var express = require('express');
var router = express.Router();
var ay = 
require('aboutyou-sdk')(100,'3ed93394c2b5ebd12c104b177b928ad0');
/* GET home page. */
router.get('/', function(req, res) {
 res.render('index', { title: 'Express' });
});
/* API */
router.get('/api/categories', function(req, res) {
 req.aboutYou.fetchCategoryTree(function(error, categoryTree) {
   var categories = categoryTree.toJSON();
   res.json(categories);
 });
});
module.exports = router;
~~~

When running your app now using npm start, you should be able to navigate over to http://localhost:3000/api/categories and should return your app’s category tree.

### Executing a product search

This was quite straightforward and you’ve got now all data to build a basic category navigation. Anyway, the real fun starts when we are able to display the actual products.

A product is a simple JSON object that can look like this:

~~~
{
  “id”: 1580329,
  “name”: “T-Shirt aus Jersey”,
  “brand”: {
    “id”: 414,
    “name”: “EDC BY ESPRIT”
  },
  “defaultImage”: {
    “url”:     “http://cdn.aboutyou.de/file/ed4745ab6847f37ec11acbf9a6002e5b"
  }
}
~~~

To get this data, we need again a new route in our application and we’re going to leverage the SDK method fetchProductSearch(criteria, callback).

~~~
router.get('/api/categories/:id/products', function(req, res) {
  var criteria = req.aboutYou.productSearchCriteria;
 
  // search criteria settings
  criteria.filterByCategoryIds(req.params.id);
  criteria.selectProductFields(['brand_id', 'default_image']);
  // execute product search 
  req.aboutYou.fetchProductSearch(criteria, function(err, searchResult) {
    if(!err) {
      var products = searchResult.toJSON();
      res.json(products);
    }
  });
});
~~~

There are various setting options for the search criteria: An overview of the available methods can you find in the documentation.

## Part 3
Congratulations, our application delivers category & product data successfully but doesn’t use any UI yet. In the last part (released very soon) of this tutorial I’m going to focus on the front-end and the interaction with our existing code.