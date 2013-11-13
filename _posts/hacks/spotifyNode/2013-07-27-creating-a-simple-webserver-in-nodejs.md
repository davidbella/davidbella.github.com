---
layout: post
title: "Creating a Simple Webserver in node.js"
description: ""
categories: [hacks, spotifyNode]
tags: ["Spotify", "Music", "node.js", "JavaScript", "Web Application", "Express"]
---

While this kind of example has probably been done to death in other posts about node, I felt it was worth its own post under this hack as this boilerplate is necessary to even get started. We will set up a basic web server in node with express, create a static public directory, and set up a few basic routes to work with to serve up some static HTML.

We'll start with the basic express setup - routes, static dir, starting the server - and fill in the details as we go.

{% highlight javascript %}
var express = require('express');

// ...

var app = express();

app.get('/songs/lookup/', function (req, res) {
  renderSongLookupForm(req, res);
});
app.get('/songs/all/', function (req, res) {
  renderSongPlayPage(req, res);
});
app.post('/songs/all/', function (req, res) {
  renderSongPlayPage(req, res);
});
app.use('/public/', express.static(__dirname + '/public/'));

app.listen(5001);

console.log('Listening on HTTP://127.0.0.1:5001');
{% endhighlight %}

Express is available from npm and can be installed locally in your project by using 'npm install express'. Express provides us with a few functions to help manage our application as a web application, notably here to listen on a port and help out with routes.

We first create to GET routes for our pseudo controller called 'songs' - lookup and all. Lookup will provide a form that allows the user to type in an artist and song title and will just grab the first available result from the Spotify web service search function. It will then redirect to the POST action of songs/all, which will display a list of all previously searched songs. The last helper we use is to create a static directory that we can store songs in and allow the user to access. Also note that routes should technically have the trailing '/' character. Since this is just a short demo, I did not research how to make this optional for an end user.

Feel free to drop this into a file - I called mine app.js - and run it with 'node app.js'. You should be able to go to 'http://localhost:5001/songs/lookup/' in your web browser and have node yell at you that you haven't yet created the 'renderSongLookupForm' function yet. This is fine, in fact, this is good since we know now that the web server is running.

Now that the basic server and routes are set up, we can go in and actually fill in the 'renderSong' functions.

To act more like a 'real' web application and framework, we are going to abstract out our HTML view from the JavaScript code when loading totally static HTML. Later on, we do generate HTML with JavaScript, but this is ugly, and in reality, we should use some templating system.

We can create a new HTML file (again, following some of the other web application best practices) in a specific 'views' subdirectory in our application. Create the following file in the base directory of the node app - 'views/song/lookup.html' - with the following HTML:

{% highlight html %}
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <head>
    <title>
      Lookup a song
    </title>
  </head>
  <body>
    <h1>
      Search
    </h1>
    <form method="post" action="/songs/all/" id="new_song" class="new_song">
      <div class="field">
        <label for="song_artist">Artist</label><br />
        <input type="text" name="artist" id="song_artist" size="30" />
      </div>
      <div class="field">
        <label for="song_title">Song</label><br />
        <input type="text" name="title" id="song_title" size="30" />
      </div>
      <div class="actions">
        <input type="submit" value="Look Up Song" id="song_submit" />
      </div>
    </form>
  </body>
</html>
{% endhighlight %}

This creates an extremely simple HTML POST form that sends a POST request to the '/songs/all/' route with two parameters - 'song_artist' and 'song_title' - we will see how to parse these out in the POST section of the '/songs/all/' route action.

In order to load and have this HTML file presented to the user we need to do a few important steps: read the HTML file in our node application and present it to the user through the HTTP response object when the user hits the '/songs/lookup' route. We can now create the 'renderSongLookupForm' function that we had referenced earlier when setting up the express server's routes.

{% highlight javascript %}
var fs = require('fs');

// ...

var newSongLookupHtml = fs.readFileSync('views/song/lookup.html');

function renderSongLookupForm(req, res) {
  res.writeHead(200, {
    'Content-type': 'text/html; charset=utf-8'
  });
  res.end(newSongLookupHtml);
}

{% endhighlight %}

Feel free to verify that the web application is now correctly responding to '/songs/lookup/' by rendering the HTML form that we just added.

The newSongLookupHtml variable is in this case a global variable that is loaded outside of the scope of returning the HTML to the browser. Also note that due to node's asynchronous nature that we are using a function called 'readFileSync' - the Sync is important to take note of since this means that this function will block node execution until it finishes. This is important for a few reasons. First, loading the HTML outside of the route request allows us to preload all HTML before the web server is even launched. Second, if, due to node being asynchronous, the response was sent back to the browser before 'newSongLookupHtml' was defined, we would have a problem - the response would include no HTML data.

The rest of the 'renderSongLookupForm' is pretty standard node web server stuff - we set the response header to be a 200, and let the browser know that we are sending some html data in utf-8. We then end our response and include the 'newSongLookupHtml' which is just the HTML form we had created earlier.

The next thing we have to handle is the POST going to '/songs/all/' from the form we just created. Again, filling in our functions from when we created our express routes, we should create 'renderSongPlayPage'. Since this function will eventually handle most of the Spotify functionality, I am going to present it as a basic function to handle the POST data instead.

{% highlight javascript %}
var qs = require('querystring');

// ...

function renderSongPlayPage(req, res) {
  res.writeHead(200, {
    'Content-type': 'text/html; charset=utf-8'
  });

  if (req.method == 'GET') {
    res.end('Not yet implemented');
  }
  else {
    var body = '';
    req.on('data', function(data) {
      body += data;
    });
    req.on('end', function() {
      var variables = qs.parse(body);

      res.end(variables.artist + ' - ' + variables.title);
    });
  }
}
{% endhighlight %}

Here, we do the standard set the HTTP header to 200 and expect HTML thing. Next, we determine if this is a GET or a POST by reading the request object's method. If we are coming from a GET - just send some text back to the browser and forget it (I don't even have this implemented towards the end of this demo either). 

Otherwise, it is a POST request (likely) and we should read out the POST data for the user. We first declare an empty variable to append data to and then we wait for the request object to give us 'data' to append. The req.on('data' tells us to wait for the request to give 'data' and to run the next parameter as a function callback - which simply tacks that data onto our personal data variable 'body'. Not until the request has finished can we be confident that all of the POST data has been read (asynchronous node again), so on end we then parse out the POST variables with querystring.

At this point, we should have enough information for a basic express web server in node with a couple of routes, and form that posts to another location (and displays the values of that POST), and an HTML view in our simple node web server. Next time, we will modify the renderSongPlayPage to interact with the Spotify web services to actually look up, download and offer links to songs.
