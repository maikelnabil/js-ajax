# AJAX and APIs

## Learning Objectives

- Explain what an API is and how to use one
- Describe AJAX and how it lets us build rich client-side applications
- Render new HTML content using data loaded from an AJAX request.
- Perform GET, POST, PUT, and DELETE requests to an API to modify data.

## Framing (5 minutes, 0:05)

Over the past few weeks, we've learned how to build front-end applications using html, css, JS, and jQuery. Yet, our applications can't persist data in databases, nor request data from databases.

Today, we're going to learn how to request information from third-party databases, and how to make our applications more dynamic by making it so our application doesn't need to refresh the page to make a request to an API server!

## Turn & Talk (10 minutes, 0:15)

> 10 minutes: 5 min T&T, 5 min review

[Open up Google Maps in your browser](https://www.google.com/maps). As you interact with the web app, consider the following questions:

1. How many times do you see the page refresh?
1. When you request new data (i.e. by searching for a location), does the page refresh? Does the Google Maps app get data from a server?

## What is an API (15 minutes, 0:30)

**API** stands for "Application Programming Interface" and is a way of describing software design. At the highest level, an API is any application with a set of instructions for how programmers can interact with it. The DOM is actually an example of an API.

When we're talking about the web (and web APIs), we're generally talking about an application that we interact with through it's routes. However, requests to an API's routes won't respond with views, they'll respond with data.

### API Data

An API will receive a scripted request and send a response. But what makes an API different from a full-stack application, is that an API won't render views, it'll just send back data. That data will generally be in one of two forms: XML or JSON.

#### XML

**XML** stands for "eXtensible Markup Language" and is the granddaddy of serialized data formats (itself based on HTML). XML is fat, ugly and cumbersome to parse. It is increasingly the less common of the two formats you'll encounter. It is still a major format due to its legacy usage across the web. You'll probably always favor using a JSON API, if available.

XML looks like this:

```xml
<users>
  <user id="23">
    <name><![CDATA[Bob]]></name>
  </user>
  <user id="72">
    <name><![CDATA[Tim]]></name>
  </user>
</users>
```

#### JSON

**JSON** stands for "JavaScript Object Notation" and has become a universal standard for sending and receiving data across the web. It is light-weight, easy-to-read and quick to parse.

JSON looks like this:

```json
{
  "users": [
    {"name": "Bob", "id": 23},
    {"name": "Tim", "id": 72}
  ]
}
```

#### What is Serialization?

All data sent via HTTP is sent as strings. Unfortunately, what we really want to pass between web applications is **structured data** (i.e., arrays and hashes). In order to do so, native data structures are **serialized**: converted from a javascript object into a string representation of the data. This string can be transmitted and then parsed back into data (de-serialized).

### Working with an API

APIs can be either public or private. If an API is public, anyone can access the data behind it. If an API is private, then you'll need to get a password (called an API Key) or go through some other form of authorization before you can access data through that API.

We'll start by exploring a public API: [PokéApi](https://pokeapi.co/)

#### You do: PokéApi (10 minutes, 0:40)

> 10 minutes

For the next 10 minutes, explore the PokéApi. Remember that web APIs take requests for data through the URL.

1. How do you get data for all Pokemon?
1. What about for a specific Pokemon?
1. How do you get data about a specific ability?

> In order to format JSON in the browser nicely, you might need a plugin like Chrome's [JSON Formatter](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa?hl=en).

## What is AJAX (15 minutes, 0:55)

**AJAX** stands for "Asynchronous JavaScript and XML".

Back in the early- and mid-1990s, the only way for a user to request new data was through the server-based request-response cycle. The user would click on a link or change some data in the UI and the whole page would reload. This was inefficient for the user and the User Experience; it was also an inefficient use of bandwidth, as an entire rendered page had to be transmitted rather than just the new or updated data.

This is where AJAX came in to play. AJAX is a way for us to make HTTP requests in JavaScript. So we can make requests to our server (asynchronously) without having to reload the page!

AJAX was first implemented in Internet Explorer as the `XMLHttpRequest` object and later adopted by Mozilla and Safari. In 2005, Gmail and Google Maps were rebuilt using `XMLHttpRequest` and a developer named Jesse James Garret wrote an article title *"[Ajax: A New Approach to Web Applications](http://adaptivepath.org/ideas/ajax-new-approach-web-applications/)"*, where he coined the term *AJAX*.

Building apps with `XMLHttpRequest` lead to a better user experience and faster applications but it was extremely verbose and cumbersome to work with. To make it easier, jQuery implemented the `.ajax()` api, abstracting away `XMLHttpRequest` into a jQuery-like set of function calls.

More recently, WHATWG (the standards body for HTML) proposed the `fetch()` api as a browser-native implementation of AJAX similar to the jQuery api.

### We Do: Lets see it in Action

We will use jQuery's `.ajax()` method to make AJAX calls to an API. The standard requests we will be making are GET, POST, PUT, PATCH and DELETE.

Let's try that out ourselves. Clone down [this repo](https://git.generalassemb.ly/ga-wdi-exercises/js-pokeapi-ajax).

In `script.js`, we will use AJAX to send a `GET` request to the Pokemon API...

```js
const url = 'https://pokeapi.co/api/v2/pokemon/1/';

// $.ajax takes an object as an argument with at least three key-value pairs...
// (1) The URL endpoint for the JSON object.
// (2) Type of HTTP request.
// (3) Datatype. Usually JSON.
$.ajax({
  url: url, // (1)
  type: 'GET', // (2)
  dataType: 'json', // (3)
}).done(() => {
  console.log('Ajax request success!');
}).fail(() => {
  console.log('Ajax request fails!');
}).always(() => {
  console.log('This always happens regardless of successful ajax request or not.');
});
```

Every AJAX request needs a URL (where we're making our request), the type (or method) of our request and the dataType that we want back (JSON or XML). jQuery's `.ajax()` method uses Promises to handle the fact that making AJAX requests is asynchronous.

### Aside: Promises & Asynchronous JS (10 minutes, 1:05)

An AJAX request is asynchronous: we'll make our request to the server and some time later will get our response. In the meantime, the rest of our code will keep running. We need some way to handle it when it's finished. We've previously handled asynchronous actions by using callbacks. jQuery's `.ajax()` method uses Promises.

You'll notice there are 3 functions chained onto the AJAX call. These are our **promise methods**. Promises are callbacks that may or may not happen. A promise represents the future result of an asynchronous operation. It's how we handle the return value of our `$.ajax` request.

- `.done()` - this code is run if the AJAX call is successful
- `.fail()` - this code is run if the AJAX call is not successful
- `.always()` - this code is always run, regardless of whether the AJAX call is successful or not

The `$.ajax()` method makes our asynchronous call to the server using `XMLHttpRequest` and returns a Promise: an object that represents some future value. If our HTTP Request is successful, it'll call `.done()`, if it isn't then it'll call `.fail()`.

### The API Response

But how do we access the JSON object we saw in the browser before? By passing in an argument to the anonymous callback. The `$.ajax` call returns a response that you can then pass in as an argument to the promise.

```js
.done((response) => {
  console.log(response)
})
```

> `response` contains the information received from the AJAX call.

We can drill through this response just like any other JS object...

```js
.done((response) => {
  console.log(response.forms.name)
})
```

## You Do: DOM Manipulation Using Response Data (10 minutes, 1:15)

1. Delete the HTML inside of `index.html` (though not the two `<script>` tags!)
1. Add a form and a `<h1>` to your HTML. Your form should include a single input for an ID and a submit button.
1. Add an event listener to your form so that when submitted, you trigger an AJAX call to the PokéAPI to find the Pokemon with the ID from the ID form field.
1. Inside your `.done()` promise callback, set the text of your `<h1>` to be the name of the Pokemon the user searched for.

> Hint: What does `.preventDefault()` do?

## Break (10 minutes, 1:25)

## AJAX and CRUD (60 minutes, 2:25)

So we've used AJAX to do an asynchronous `GET` request to an API. More often than not, 3rd party APIs are read-only. They don't want anyone updating the Pokemon! There are some APIs that you can do full CRUD on, just not for the PokeApi.

It just so happens that we at GA have built a [Tunr API for artists and songs](https://ga-tunr-ajax.herokuapp.com/artists.json), where we can do full CRUD with AJAX.

To start, open your Terminal, and `cd` to your sandbox, then clone [this repo](https://git.generalassemb.ly/ga-wdi-exercises/tunr-ajax-jquery/):

```bash
git clone git@git.generalassemb.ly:ga-wdi-exercises/tunr-ajax-jquery.git
```

Then `cd` into this repo, and open it on your text editor. We will be working in the `script.js` file. You should see this in the `script.js` file:

```js
$(document).ready(() => {
  $('.js-get').on('click', e => {
    e.preventDefault();
    console.log('clicked!');
  });

  $('.js-post').on('click', e => {
    e.preventDefault();
    console.log('clicked!');
  });

  $('.js-put').on('click', e => {
    e.preventDefault();
    console.log('clicked!');
  });

  $('.js-delete').on('click', e => {
    e.preventDefault();
    console.log('clicked!');
  });
});
```

We can now use `$.ajax()` to make asynchronous HTTP requests to our Tunr API!

We're going to replace the code inside our four event handlers with 4 AJAX calls to our API.

### Get
Lets start with our `GET` request. This won't look too different from the one we created for the PokeApi before:

<details>
  <summary>AJAX `GET` request:</summary>

```js
$('.js-get').on('click', () => {
   $.ajax({
     type: 'GET',
     dataType: 'json',
     url: 'https://ga-tunr-ajax.herokuapp.com/artists'
   }).done((response) => {
     console.log(response)
   }).fail((response) => {
     console.log('Ajax get request failed.')
   })
 })
```

</details>

### Post
To make a post request, we'll change the method type to `POST` and add a `data` sub-object.

<details>
  <summary>AJAX `POST` request:</summary>

```js
$('.js-post').on('click', () => {
  $.ajax({
    type: 'POST',
    data: {
      artist: {
        name: 'Limp Bizkit',
        nationality: 'USA',
        photo_url: 'http://nerdist.com/wp-content/uploads/2014/12/limp_bizkit-970x545.jpg'
      }
    },
    dataType: 'json',
    url: 'https://ga-tunr-ajax.herokuapp.com/artists'
  }).done((response) => {
    console.log(response)
  }).fail((response) => {
    console.log('AJAX POST failed')
  })
})
```

</details>

### Put
To make a `PUT` request, we need the updated data we want to store in our database. We'll send it to the route for the item we want to update, i.e. `https://ga-tunr-ajax.herokuapp.com/artists/6`.

<details>
  <summary>AJAX `PUT` request:</summary>

```js
$('.js-put').on('click', () => {
    $.ajax({
      type: 'PUT',
      dataType: 'json',
      url: 'https://ga-tunr-ajax.herokuapp.com/artists/6',
      data: {
        artist: {
          name: 'Robert Goulet',
          nationality: 'British',
          photo_url: 'http://media.giphy.com/media/u5yMOKjUpASwU/giphy.gif'
        }
      }
    }).done((response) => {
      console.log(response)
    }).fail(() => {
      console.log('Failed to update.')
    })
  })
```

</details>

### Delete
Making a `DELETE` request only requires changing our method type and making our request to the artist we want to delete.

<details>
  <summary>AJAX `DELETE` request:</summary>

```js
$('.js-delete').on('click', () => {
  $.ajax({
    type: 'DELETE',
    dataType: 'json',
    url: 'https://ga-tunr-ajax.herokuapp.com/artists/6'
  }).done(() => {
    console.log('DELETED')
  }).fail(() => {
    console.log('Failed to delete.')
  })
})
```

</details><br />

**NOTE:** You can find the solution to this exercise [here](https://git.generalassemb.ly/ga-wdi-exercises/tunr-ajax-jquery/blob/solution/script.js)!

## Closing (5 minutes, 2:30)

JavaScript developers often need to perform CRUD functionality to/from an API. Today we've used jQuery to do this, but there are other smaller libraries that exclusively handle interacting with API endpoints. The most popular library right now is one called axios, which we'll use later on in the course. There is also the increasingly-popular `fetch()` method, which we have an optional bonus for below.

-------

## Bonus

### API Keys

[Read More](/apiKeysExercise.md)

### Fetch, jQuery, XMLHttpRequest

[Read More](/fetch-jquery-xml.md)

### CRUD with Fetch

[Read More](/fetch-crud.md)

### jQuery AJAX Cheat Sheet

[Read More](/jQuery-ajax-cheatsheet.md)
