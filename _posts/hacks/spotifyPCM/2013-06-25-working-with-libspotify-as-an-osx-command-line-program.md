---
layout: post
title: "Working with Libspotify as an OSX Command Line Program"
description: ""
categories: [hacks, spotifypcm]
tags: ["C", "Spotify", "OSX"]
---
{% include JB/setup %}

This is based off of Damien Radtke's excellent post that walks through most of the boilerplate of working with the API: http://damienradtke.org/playing-with-the-spotify-api/. This post will discuss working with the Spotify API from the command line on OSX, and using CoreAudio as opposed to ALSA to play music.

The Spotify API documentation mentions that the examples weren't really built to be made and run from the command line, but gave a few tips as to how to accomplish this. I am not very familiar with OSX specific development, so I passed over most of this.

I started with a very basic program that simply referenced the API header:

{% highlight c %}
#include <stdio.h>
#include <libspotify/api.h>

int main()
{
    printf("Included the libspotify/api.h\n");
    return 0;
}
{% endhighlight %}

One of the suggestions was to install the framework in /Library/Frameworks/ - after some fruitless attempts to figure out how to "install a framework" on OSX, I realized that it was really just to recursively copy the libspotify.framework to /Library/Frameworks. Now we can use the gcc command line switch -framework to link in the spotify API.

    gcc -framework libspotify program.c


