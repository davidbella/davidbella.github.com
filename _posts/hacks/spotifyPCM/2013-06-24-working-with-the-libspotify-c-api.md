---
layout: post
title: "Working with the Libspotify C API"
description: ""
categories: [hacks, spotifyPCM]
tags: ["C", "Spotify", "Music", "PCM"]
---
{% include JB/setup %}



So I finally managed to get the Spotify data returned from their API piped into a file to play as a regular sound file.

Basically the .music_delivery callback in the Spotify API is called whenever the API returns a chunk of music data. This music data is (I would assume typically) 16 bit signed, dual channel, 44100 Hz PCM audio data. The example program I was working with from Damien Radtke's website was just playing the music when it was received, same thing goes for the jukebox example that came with the API. However, I was interested in seeing what the data actually was, and how I could store it or convert it into an .mp3 file (for demonstration purposes only, I believe it is against the Spotify ToS to do this).

Working off of this simplified example of the jukebox program that came with the Spotify API: http://damienradtke.org/playing-with-the-spotify-api/ 

The main key was capturing the data chunks as they came back from the .music_delivery callback. I used the fwrite command to take the raw data and write it out to a file (or stdout if preferred).

     FILE* outputFile;
     outputFile = fopen("song.data", "ab");
     fwrite(frames, 1, num_frames * format->channels * 2, outputFile);
     fclose(outputFile);

I dropped this into the .music_delivery callback function (I realize that I keep redeclaring outputFile, but this is just an example). What this does is basically, create an output FILE file handle, initialize it as "song.data" and open it in append mode and writing binary data.

The fwrite here is key, the parameters are as follows:

     ptr - "frames" is the song data returned from the API callback
     size - set to 1, this was a lucky guess I think but since the callback probably returns an array of byte data, a good guess
     count - number of elements, this was another key. Number of bytes, times number of channels, times two (why? I don't know)
     stream - outputFile FILE of course

This random file I found through long hours of googling helped me to have the "ah ha" moment about the "count" parameter in fwrite: http://www.ee.iitb.ac.in/daplab/resources/wav_read_write.cpp
Another resource that helped here, though there isn't a full explanation in the answer: http://stackoverflow.com/questions/14347450/spotify-raw-output-sounds-garbled-when-interpreted-as-pcm  

So we have captured our data! Great. Now what? Play it!

I used sox, but I am sure you could use lame or some other encoding program to check it out:

    > mv song.data song.s16
    > play -c 2 -r 44100 song.s16

sox requires the file have the proper extension (there may be a switch for this too) and to set the channels to 2 and rate to 44100 since this is headerless PCM data.

And there is your song, playing through the console as PCM data.

A couple of wrap up points:

I still have one question - in the fwrite call, why do I need to multiply the count parameter by two? I would have assumed that format->channels was two already since it is stereo data.

What's the best way to get this into a web application (which is one of my further goals for working on this)? My initial research is pointing me towards NodeJS, in particular TooTallNate's repos. Seems he has plenty of work done on this already, including Spotify integration already made. I should be able to leverage this heavily for my further needs.