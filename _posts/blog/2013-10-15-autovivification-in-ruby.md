---
layout: post
title: "Autovivification in Ruby"
description: ""
categories: "blog"
tags: ["ruby", "perl", "autovivification", "hashes"]
---

**What is Autovivification?**  
Autovivification is the concept that a hash style data structure can make inferences about its internal structure as it is being created. For instance, creating a nested hash under one of the keys would normally require you to explicitly state that you wanted to create a hash there. Autovivification allows you to skip this step and create the nested structure on the fly.

To make the idea more tangible, here is a quick code example in Perl that automagically fills out a hash:

{% highlight perl %}
  use strict; # don't think I'll ever break this Perl habit...
  use warnings;

  use Data::Dumper;

  my %shares;

  my $portfolio = "Personal Portfolio";
  my $symbol = "GOOG";
  my $date = 20131015;

  $shares{$portfolio}{$symbol}{$date} = 50;

  print(Dumper(\%shares));
{% endhighlight %}

{% highlight perl %}
  $VAR1 = {
    'Personal Portfolio' => {
      'GOOG' => {
        '20131015' => 50
      }
    }
  };
{% endhighlight %}
_After using Ruby for 4 weeks, I'll have you know I forgot every single semi-colon when writing this_

In Ruby, a similar piece of code could look like this:

{% highlight ruby %}
  require 'awesome_print'

  shares = {}

  portfolio = "Personal Portfolio"
  symbol = "GOOG"
  date = 20131015

  shares[portfolio] = {}
  shares[portfolio][symbol] = {}
  shares[portfolio][symbol][date] = 50

  ap shares
{% endhighlight %}

{% highlight ruby %}
  {
    "Personal Portfolio" => {
      "GOOG" => {
        20131015 => 50
      }
    }
  }
{% endhighlight %}

Notice how we had to create hashes down the way as we went? Not with autovivification.

**A Bit More Background**  
The idea was introduced in the Perl programming language as default, but can be written into other languages. As a former Perl programmer who relied on this feature heavily, I thought I would do that comparison between Perl and Ruby, how we can make Ruby act similarly, and when to use autovivification in Ruby (hint: probably never).

**Autoviv in Ruby**  
The Wikipedia article on Autovivification has a section on [how to do it in Ruby](http://en.wikipedia.org/wiki/Autovivification#Ruby). I have copied over that example, but spread it out to try to make it a bit more readable.

{% highlight ruby %}
  tree = proc do
    Hash.new do |hash, key|
      hash[key] = tree.call
    end
  end

  autovivifying_hash = tree.call
  autovivifying_hash["port"]["sym"]["date"] = 50 #=>
  # {
  #   "port" => {
  #     "sym" => {
  #       "date" => 50
  #     }
  #   }
  # }
{% endhighlight %}

Let's break it down into the three main lines, which cover three very important pieces to how this works.

**1. Create a proc**  
We'll go into more detail on this in section 3, but we need a way to save this block of code, and we know in Ruby that we can save a block by creating it as either a proc or lamdba.

**2. Override the Newly Created Hash's Behavior**  
According to Ruby's documentation on creating a new hash, you can set the default value to be any item if passed in as a parameter. However, Ruby offers another, [more powerful option](http://ruby-doc.org/core-2.0.0/Hash.html#method-c-new). If you pass in a block, you can have access to the hash object and the key, and set the default value yourself. This allows us to do something very important, as we will see in magical step 3.

**3. Set the Key to Another Autovivificious Hash**  
Since we have access to the hash and the key in the block we passed to Hash.new, we can now define the default value for that hash key we just tried to access. How about an autovivifying hash? Oh, we have a way to make one of those already - the proc we are writing! Let's call it! So now, anytime we try to access a nil key in our autovivificious hash, we will create there instead of nil - another autovivificious hash!

**Yeah, But Why Can't We Just Give a Default Value of Hash.new?**

{% highlight ruby %}
  not_auto_hash = Hash.new(Hash.new)
{% endhighlight %}

So now the "not_auto_hash" hash will set its keys to be a new hash every time right? Sort of. It will create a new hash only for _the first level of keys_. Okay, sure, so let's go ahead and do the same for the second level of keys...

{% highlight ruby %}
  not_auto_hash = Hash.new(Hash.new(Hash.new))
{% endhighlight %}

But wait... that's only two keys deep...

{% highlight ruby %}
  not_auto_hash = Hash.new(Hash.new(Hash.new(Hash.new))) # ad nauseam...
{% endhighlight %}

This is approximately what our "tree" proc is doing up above, simply calling Hash.new each time a new key depth is needed. It just does so with a fancy looking recursive proc.

**When Should I Use This?**  
I'm not really certain on the Ruby community's consensus of autovivification, but I am going to go out on a limb and say don't do this, unless there is a very good reason. From my time working mostly with Perl, I can say that it is sometimes dangerous using this feature, unless it is absolutely clear what data is entering the hash, when, and how. From Ruby, the first impression that I got was that Ruby would get very confused when you tried to treat a trailing [key] accessor as an array, instead, the autoviv thought it was being clever and would create a hash instead.

**Some Follow Ups**  
The research here led me to a few interesting resources that I would like to talk about in another post. Namely: [Rubinius](rubini.us) and [Ruby Facets](rubyworks.github.io/facets/‎). I would also like to mess around with overriding some defaults - Can I make the hash class autovivify by default? Can I have the curly braces take over for square braces to look more like Perl? Can I have the square braces ignored so that I don't confuse it with an array? I do think two blog posts on autoviv are two more than necessary, so that will probably be all.
