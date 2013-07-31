---
layout: post
title: "Working with the Spotify API in Node"
description: ""
categories: [hacks, spotifyNode]
tags: ["Spotify", "Music", "node.js", "JavaScript", "Web Application", "Express"]
---
{% include JB/setup %}

This post will outline some basic interactions with the Spotify API using TooTallNate's Node Spotify package.

Building on from the first post in this series, we should already have a working web server with some basic routes set up. This post will expand upon the 'POST' section of the '/songs/all/' route by allowing the posted data to be looked up with the Spotify API and stored locally. While this is likely against Spotify's ToS in some way, it is only for demonstration purposes and I encourage you support your favorite artists by listening to them through Spotify or buying their albums.

We left off in the 'renderSongPlayPage' function last time, where we pulled the POST data from the request and displayed them out on the client's browser. We are basically going to keep dropping more and more code into this spot until we have queried Spotify, got the song data, pulled it down, and displayed a list of all songs available back to the client. We'll end up nesting a bunch of functions and callbacks together, something that I will eventually want to learn how to abstract out since I get the feeling Node can get like this a bit.

First thing is to include TooTallNate's 'spotify-web' module through npm and in your server code. Then we can use the first of our soon-to-be-very-nested functions to log in to Spotify.

{% highlight javascript %}
var Spotify = require('spotify-web');

var username = 'YourSpotifyUsername';
var password = 'YourSpotifyPassword';

// ...

Spotify.login(username, password, function (err, spotify) {
  if (err) throw err;

  // ...
});
{% endhighlight %}

Nothing too exciting here. We log in to the Spotify service with our Spotify username and password (you are a paying member right? I'm actually not sure if you need a subscription to use this part of the API). The last parameter of the login function is the callback - the function that will run when the Spotify.login function completes. The login function gives you two parameters: an err for if an error occurred and a 'spotify' instance that can be used throughout the rest of the work you need to do with Spotify.

At this point, we can craft a search query and use it to search for the parameters the client just provided to the HTML form:

{% highlight javascript %}
queryOpts = { query: variables.artist + ' ' + variables.title, type: 'tracks' };

spotify.search(queryOpts, function (err, xml) {
  if (err) throw err;

  // ...
});
{% endhighlight %}

We craft a querystring object using the POST data and we also specify the type of Spotify item we want to search for - in this case tracks - but we could search for artists, albums, etc. if we wanted to. Once we call spotify.search (note we are using lowercase spotify - the object returned in the login callback) we are again dumped into a callback to run upon its completion.

This time, in addition to the standard 'err' parameter, we also get the raw 'xml' that gets returned from the web service. It is up to us as the consumer to parse this out as we see fit. This was the first time I had run into a more complexly structured JSON object that I had to manually work through, so forgive some of the laziness here. Normally, we would want to verify things exist instead of just blindly grabbing what we expect to be there. While this approach works for the prototype here, I wouldn't put anything like this in a production ready application.

{% highlight javascript %}
var xml2js = require('xml2js');

var parser = new xml2js.Parser();
parser.on('end', function (data) {
  var trackUri = Spotify.id2uri('track', data.result.tracks[0].track[0].id[0]);

  // ...
});

parser.parseString(xml);
{% endhighlight %}

Node asynchronicity at work again here with the xml2js parser to convert that returned XML into a JSON object. Once the parser is done parsing the XML string, we can use the 'data' parameter in the callback (the JSON version of the XML). At this point we use the Spotify helper function to convert a track ID to a URI. The URI will allow us to find the actual track object. Spotify URIs are also useful as web links to open Spotify with a specific track or artist page.

The last step is to actually request the track from Spotify based on the URI and pipe it to a file.

{% highlight javascript %}
spotify.get(trackUri, function(err, track) {
  if (err) throw err;

  var file = fs.createWriteStream('public/' + trackUri + '.mp3');

  track.play()
    .pipe(file);
});
{% endhighlight %}

We use the track's play function to get the song data and pipe that into a file stream pointing to a file in the public folder.

Since this post has gone on for a while I will save the last bit, reading the contents of a directory and displaying them on the client, for a final posting later.
