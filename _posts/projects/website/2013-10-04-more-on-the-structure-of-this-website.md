---
layout: post
title: "More on the Structure of this Website"
description: ""
categories: [projects, website]
tags: ["Jekyll", "Jekyll Bootstrap", "GitHub", "Website"]
---
{% include JB/setup %}

### About

I haven't looked at this website or its structure for the past few months, so looking back at it 4 months later, I had a few questions about how I had wanted to set it up. Luckily, it still makes some sense to me, and my previous post in June documented the structure enough for me to get a grasp on what I was trying to accomplish.

I want to expand on that post and really try to explain the structure and the tagging system. Note that I will be writing loosely about how Jekyll works, but the disclaimer here is: I don't know how Jekyll works. I'm just going to fake it and speak on a somewhat abstract level.
<br/> 
<br/> 
#### Jekyll Knows More About Your Site Than You Do

Before jumping into some of the specific ideas for organization I had way back in June, here is a short blurb about how Jekyll pulls together all of the pages and posts of the site:

When Jekyll reads through your files, it parses the YAML Front Matter at the top of those files to pull out some metadata (programmers love metadata!). This data is then accessible in some fashion through the "site" object (or collection, or hash, or whatever Jekyll stores it as internally). The YAML Front Matter can contain all kinds of information, but some of the most common are the layout of the markdown file (is it a post? is it a page? is it some other kind of file?), the title, categories, and tags.

This site object is then usable across your markdown files (pages, posts, etc.) to create static page content based on all the metadata Jekyll has pulled about your site.

For a concrete example, the home of this site uses this bit of Jekyll wizardry to generate a list of _every post that I have written_

{% highlight ruby %}
  for post in site.posts
{% endhighlight %}

From here, we can inject things like: "post.title" or "post.categories" right into our markdown, which Jekyll will translate into things like "More on the Structure of this Website" (How meta!).
<br/> 
<br/> 
#### Expanding On The Points From Last Time

##### All markup files in the \_posts folder are considered

Recursively! Deep dive! Awesome! I'm an organization freak, so I knew right away that having one giant folder of \_posts to hold everything wasn't going to fly for me. Great, Jekyll can find what it needs, but what about _me_?

At the time of this writing, the directory structure of \_posts looked like this:

![Directory Structure]({{ site.url }}/assets/projects/website/directory_structure.png)

Let's take a quick peek at the YAML Front Matter one of the posts - How about **2013-07-27-creating-a-simple-webserver-in-nodejs.md**?

{% highlight yaml %}
---
layout: post
title: "Creating a Simple Webserver in node.js"
description: ""
categories: [hacks, spotifyNode]
tags: ["Spotify", "Music", "node.js", "JavaScript", "Web Application", "Express"]
---
{% endhighlight %}

Notice here that the list of categories is **\[hacks, spotifyNode\]** -- which lines up nicely to _where this file is in the directory structure_. Also, [the default URL structure for Jekyll](http://jekyllrb.com/docs/permalinks/) makes it such that our URL points nicely to:

{% highlight yaml %}
http://davidbella.github.io/hacks/spotifynode/2013/07/27/creating-a-simple-webserver-in-nodejs/
{% endhighlight %}

So far so good, but the real magic happens now. We can use these nicely tagged categories to _selectively display posts based on their category_. Whoa. So I can create [a page dedicated entirely to the spotifyNode hack]({{ site.url }}/pages/hacks/spotifyNode) that will list only posts that are categorized as part of the spotifyNode hack! Here is the Jekyll YAML Front End VooDoo:

{% highlight ruby %}
for post in site.categories.spotifynode
{% endhighlight %}

How _exactly_ Jekyll stores this is a mystery to me, for now, but this handy iterator finds all the posts relevant to a given category.

##### Using YAML Front Matter, you can tag posts and pages in any way you want. You can then reference them off of the main “site” object in the liquid templating engine.

I don't think I was being very clear here so I am going to try to explain this better. I started to hint at this a bit towards the end of the last point, but the idea is again one of organization.

Take a quick pick a bit further up this page at the image of my directory structure for \_posts. Now take a look at this image of a directory structure for something I am calling "pages":

![Page Structure]({{ site.url }}/assets/projects/website/page_structure.png)

Look familiar? Now I can store arbitrary types of pages for each of the different hacks or projects I might have going on. Right now it is only an index.html which runs the nifty "for post in site.categories.(whatever)", but in the future there could be demo pages, sales pitches, whatever. Especially for web based projects that might need somewhere to land (granted, static only due to the nature of Jekyll), but whatever. It is _flexible_ which is what I enjoy about it.

#### Conclusion
Anyway, I guess none of this matters all that much. The takeaway here is really that even a simple static site generator like Jekyll can be powerful, flexible, and customizable to whatever the author needs. Using markdown, YAML Front Matter, easy syntax highlighting, GitHub pages integration, etc. makes it a great choice for programmers who enjoy flexibility, metadata, and templating to make their lives easier (and more fun).