---
layout: post
title: "Displaying a list of mp3s in node.js"
description: ""
categories: [hacks, spotifyNode]
tags: ["Spotify", "Music", "node.js", "JavaScript", "Web Application", "Express"]
---

Last time, we left off having written the song file to the server's disk. In fact, we didn't even send the response end so the browser is probably just hanging in limbo wondering what is going on. We'll remedy this by sending the response containing a list of songs in the public/ folder!

Let's just make this simple and throw a finish callback on the writing of the file to the server:

{% highlight javascript %}
track.play()
.pipe(file)
.on('finish', function() {
  fs.readdir(process.cwd() + '/public', function (err, files) {
    if (err) throw err;

    // ...
  }
}
{% endhighlight %}

Note that unlike in the beginning when we use a synchronous (blocking) function to read in the static HTML files, we are doing this asynchronously. I think this was about the time that I started getting the hang of Node's asynchonicity and wanted to just play with it. Anyway, we can use the 'process.cwd()' function to get our root directory and slap a '/public' on to the end to get the directory we are going to serve the tunes out of.

The next part gets a little interesting. The callback from 'fs.readdir' provides us with an array of files that we can go through. As you would expect, we want to go through each file, get the path to it, and perhaps build out a string of data to return to the browser. My first (synchronous) instinct was to use a simple for loop, but this isn't synchronous! It wouldn't block execution, and the response.end method after the for loop would just get called before I had gathered enough data from the for loop. To solve this, I found a module called 'async' from caolan's github which offered something more like what you would expect to see in a for loop in Node - an on finish callback.

{% highlight javascript %}
var mp3file = new RegExp('.mp3$');

async.each(files, function(file, callback) {
  if (mp3file.test(file)) {
    spotify.get(file.replace('.mp3', ''), function (err, track) {
      html += '<a href="http://' + request.headers.host + '/public/' + file + '">' + track.artist[0].name + ' - ' + track.name + '</a><br />';
      callback();
    });
  }
  else {
    callback();
  }
},
function (err) {
  if (err) throw err;

  html += '</p><br /> <a href="http://' + request.headers.host + '/songs/lookup/' + '">Find Another Song</a>';

  response.end(html);
});
{% endhighlight %}

So there is a lot going on here, but breaking it down, it isn't that different from your standard for loop.

The first thing we do is create a RegExp to match any file that has a '.mp3' at the end as its file extension.

Next up we call the magical 'async.foreach' - this one took me a while to figure out since I am not very familiar with JavaScript and the documentation didn't have an actual example to read off of. The key to it is really in the second parameter. But first, the first parameter is easy - it is our collection (array) of files.

The second parameter is a callback with each item in the array and a callback to let it know that you are done with that item. So this is where we run the code that would be in the typical 'for' block. In our case, we want to give a call to the Spotify web service again and find out just what these mp3 files are. Since we originally stored them by their URI, we can just chop off the .mp3 extension and look it up. It may be a better idea in a real application to use some kind of server database to keep and store the information, but this is just a short example so we should be fine making a couple extra calls. It is also vital to actually call the 'callback()' function so async knows we are done with that item. Note we are calling callback() in two places - the call to spotify.get is async again and the if statement isn't!

Anyway, once we get the details back on the track URI from Spotify, we build out an html string that I never told you to create before. You'll want to scope that appropriately so that we can keep using it here. This will create one line for each song in the public folder, with an 'a href' tag to the location of the song in the public directory. Note that we can use the 'request.header.host' to pull out the URL we are hitting.

Finally we can send the response.end method with all the HTML we generated!

In conclusion, there is certainly a lot we can do here better. Most notably are better abstraction/structuring of the app, local storage of metadata for downloaded songs, templating instead of generating all that HTML, and on and on. But not too bad for a quick demo!
