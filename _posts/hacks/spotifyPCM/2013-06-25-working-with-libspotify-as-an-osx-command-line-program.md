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

    $ gcc test.c
    test.c:2:28: error: libspotify/api.h: No such file or directory

So it seems we can't find what "libspotify/api.h" is in our program. We haven't yet put the libspotify code into our include path so let's figure out how to do that. I am slightly more familiar with Linux and I believe we could work some magic with pkg-config, but on OSX I believe the equivalent is to make it a "framework".

One of the suggestions from the Spotify README was to install the framework in /Library/Frameworks/ - after some fruitless attempts to figure out how to "install a framework" on OSX, I realized that it was really just to recursively copy the libspotify.framework to /Library/Frameworks/. Our previous compilation now succeeds!

    $ gcc test.c
    $ ./a.out
    Included the libspotify/api.h

This works great until we actually want to use any of the functions given to us by the API! The reason being that the header file is now available so the compiler knows about it, but the linker doesn't know where the implementation is. We can use the gcc command line switch -framework to link in the spotify API.

    gcc -framework libspotify test.c

This is really all we need to write some basic C programs on the OSX command line that use the Spotify API, but let's go ahead and follow the rest of Damien's introduction and play the audio through the console. In his example, he uses ALSA, the Advanced Linux Sound Architecture, to play audio - which we realize right away may not work for us on OSX. Luckily, the Spotify examples contained a file called osx-audio.c which serves as a drop in replacement for the ALSA code.

Once we get to the section of Damien's code that begins playing audio (around the part where we copy the extra audio files out), instead of using the ALSA file, bring out the OSX file. This file also contains the "audio_init" function which is critical to playing sound. Of course, since we started using them, we have to include them on our command line call:

    gcc -framework libspotify osx-audio.c audio.c test.c

ALmost there! This is a similar error we received when we tried to compile earlier without linking the libspotify framework on the command line. It took me a while to figure out which framework needed to be included, but after some searching on google, the answer was revealed as "AudioToolBox".

    gcc -framework libspotify -framework AudioToolBox osx-audio.c audio.c test.c

If you had followed along with Damien, you should be able to type in an artist, a song, and start playing audio through your OSX command line!
