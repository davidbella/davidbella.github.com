---
layout: post
title: "Creating a page on GitHub Pages with Jekyll"
description: ""
categories: [projects, website]
tags: ["Jekyll", "Jekyll Bootstrap", "GitHub", "Website"]
---
{% include JB/setup %}

### About

I was pleasantly surprised how simple it was to get this page up and running thanks to Jekyll, Jekyll Bootstrap, and GitHub Pages. I won't rewrite how to get up and running since this website does an excellent job already - http://jekyllbootstrap.com/. If you have a GitHub account, it is as easy as creating a new repository, cloning the bootstrap repository, and uploading it to your new repository. From here, customizations can be made as desired.

One of the first things that I did personally on my Jekyll site (this one), was to remove a lot of the things that I found unnecessary. This site is inspired by http://justinjackson.ca/words.html and I do want to keep it as simple as possible and let the content do the work. That all being said, I do want some level of control, customization, styling, and something of a playground for something that I do work with in my professional life - web development.

Aside from killing unnecessary links, I have restyled this page very, very slightly from the default bootstrap and removed most of the things that I see aren't useful at this point (tags, categories, pages, comments, analytics, etc.). I'm sure there are a few places with some lingering dead links, but I will continue to clean them up as I find them.

### Quick Customizations

One thing that I am obsessed with and didn't quite see in the default Jekyll setup is greater organization. Jekyll does indeed, so far, offer all the tools that are required to customize the organization of content through a couple of points

* All markup files in the posts folder are considered, regardless of directory structure. This is key for my organization so that I can physically separate posts by overall topic (such as "Projects/Website") and not look at some huge unstructured base of posts.
* Using YAML Front Matter, you can tag posts and pages in any way you want. You can then reference them off of the main "site" object in the liquid templating engine. So you can create an arbitrary attribute (I personally used "type") to show that this page is of "type" "website". This can be used to delineate pages from each other

I am still new on this journey of building a personal site, but Jekyll Bootstrap has made the experience pretty easy so far.