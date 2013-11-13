---
layout: post
title: "Visualizing Your Programs"
description: ""
category: "blog"
tags: ["Blog", "Visualization", "Programming", "Ruby"]
---

**It's All About Your Data Structures**  
I think one key to being able to read hashes and other data structures effectively is being able to visualize them on the fly. Knowing where you are in the code, what you are operating on, and what items you have available to you at all times is a super powerful feeling. Here are a few tips I use to help me put the flow back into my workflow.
<br/>
<br/>

**The Power of Printing**  
It seems that people sometimes put down the idea of using print statements to debug code, but I believe it is an effective enough tool for short projects. Specifically to Ruby, I try to avoid using the 'puts' method when looking at an item. 'puts' is designed for engaging the user and presenting them information. A good alternative is simply using 'p'. Calling 'p' with the item as a parameter will list all the details of that item as you the programmer would expect to see them. For very complicated structures, two alternatives I have seen are [Pretty-printer - 'pp'](http://ruby-doc.org/stdlib-2.0.0/libdoc/pp/rdoc/PP.html) and [Awesome Print - 'ap'](https://github.com/michaeldv/awesome_print). These gems format and colorize output into something more human readable. After all, we are all humans, right 'UserAgent: Spider'?

A more powerful approach would be to use [pry](http://pryrepl.org/) - I am not going to go into too much detail about pry since the point is really just this:

*Look at what gets printed out*

This is the number one most powerful thing to do. Rerun code with a print statement. Immediately. Before sitting there and scratching my head for 20 minutes, I like to get a feel for what I am working with by simply printing them out. If I'm still in the Ruby-hates-me stage:

*Read and follow the error message*

Can't call .count on a nil object? Guess what, I probably didn't get that Array I was expecting to get. Why not?
<br/>
<br/>

**What Do My Data Structures _Look_ Like**  
**Simple Array of Strings**  
We are going to start with a possibly contrived and simple example and look at just a basic Array of Strings.

{% highlight ruby %}
["Four Tet", "Burial", "Thom Yorke"]
{% endhighlight %}

Seems pretty innocuous, nothing out of the ordinary. However, there are a few comments worth making. Each of those strings are objects of type String. The array is holding a _fairly advanced data type_ in each of those Strings. In fact, and I don't know the details of Ruby's String class, I am venturing that each of these Strings _themselves_ hold an Array of characters of some sort. Look at that! We've been using an Array of Arrays all along and didn't even know it!

{% highlight ruby %}
["Four Tet".chars, "Burial".chars, "Thom Yorke".chars] #=> [["F", "o", "u", "r", " ", "T", "e", "t"], ["B", "u", "r", "i", "a", "l"], ["T", "h", "o", "m", " ", "Y", "o", "r", "k", "e"]]
{% endhighlight %}
(Confession: I had no idea this would work when I started writing it - I just popped into IRB and you guessed it, ran a few test statements)

**Nested Structures**  
This part may seem a bit long, but I think it's important to really show how long this thought process is for me, especially when I get lost somewhere and need to back up and slow down.

It's really hard to just guess what is happening when iterating, potentially many levels deep, through a complicated data structure of hashes and arrays. My strategy is to go down a level and print out everything available to me, just to get an idea of where I am.

Here is an abridged version of part of an assigment stolen from Flatiron schoolwork:

{% highlight ruby %}
pigeon_data = {
  :color => {
    :purple => ["Theo", "Peter Jr.", "Lucky"],
  },
  :gender => {
    :male => ["Theo", "Peter Jr.", "Lucky"],
    :female => ["Queenie", "Ms .K"]
  }
}
{% endhighlight %}

The first step is to just start at a high level. Just go one level into the hash to get a feel for what we are working with:

{% highlight ruby %}
pigeon_data.each do |mystery_key, mystery_value|
  p "Key class: #{mystery_key.class}"
  p "Value class: #{mystery_value.class}"
end

#=>
"Key class: Symbol"
"Value class: Hash"
"Key class: Symbol"
"Value class: Hash"
{% endhighlight %}

This is a good start, now we know that we have keys of type symbol (as we expected), and we also know that we have values of type _Hash_. Let's change this up a little bit to see if we can apply a bit more meaning to our variable names:

{% highlight ruby %}
pigeon_data.each do |mystery_key, mystery_value|
  p "Key info: #{mystery_key}"
  p "Value info: #{mystery_value}"
end

#=>
"Key info: color"
"Value info: {:purple=>[\"Theo\", \"Peter Jr.\", \"Lucky\"]}"
"Key info: gender"
"Value info: {:male=>[\"Theo\", \"Peter Jr.\", \"Lucky\"], :female=>[\"Queenie\", \"Ms .K\"]}"
{% endhighlight %}

So, the key we can clearly see looks like some kind of attribute. It might be good for us to rename the key in this iteration to be something more clear - I'm going to suggest attribute_name. The value? Well, we don't really know enough about it yet, so let's just call it the attribute_hash and keep digging in:

{% highlight ruby %}
pigeon_data.each do |attribute_name, attribute_hash|
  attribute_hash.each do |mystery_key, mystery_value|
    p "Key class: #{mystery_key.class}"
    p "Value class: #{mystery_value.class}"
  end
end

#=>
"Key class: Symbol"
"Value class: Array"
"Key class: Symbol"
"Value class: Array"
"Key class: Symbol"
"Value class: Array"
{% endhighlight %}

Great! We are seeing the light at the end of the tunnel here with the arrays. Let's grab the values here and apply meaning to these variables as well:

{% highlight ruby %}
pigeon_data.each do |attribute_name, attribute_hash|
  attribute_hash.each do |mystery_key, mystery_value|
    p "Key class: #{mystery_key}"
    p "Value class: #{mystery_value}"
  end
end

#=>
"Key class: purple"
"Value class: [\"Theo\", \"Peter Jr.\", \"Lucky\"]"
"Key class: male"
"Value class: [\"Theo\", \"Peter Jr.\", \"Lucky\"]"
"Key class: female"
"Value class: [\"Queenie\", \"Ms .K\"]"
{% endhighlight %}

So now we know that the keys here will represent the actual attribute value we are looking at, and the value is a list of names. Let's go through the array, and print out all of the information we have in the final nested code:

{% highlight ruby %}
pigeon_data.each do |attribute_name, attribute_hash|
  attribute_hash.each do |attribute_value, names|
    names.each do |name|
      puts "#{name} has #{attribute_name} of #{attribute_value}"
    end
  end
end

#=>
Theo has color of purple
Peter Jr. has color of purple
Lucky has color of purple
Theo has gender of male
Peter Jr. has gender of male
Lucky has gender of male
Queenie has gender of female
Ms .K has gender of female
{% endhighlight %}

How cool is that! Let's go through it one more time and print it out so that it _looks_ like the actual hash we started with:

{% highlight ruby %}
pigeon_data.each do |attribute_name, attribute_hash|
  puts "#{attribute_name}"
  attribute_hash.each do |attribute_value, names|
    puts "  #{attribute_value}"
    print "    "
    names.each do |name|
      print "#{name} "
    end
    puts
  end
end

#=>
color
  purple
    Theo Peter Jr. Lucky
gender
  male
    Theo Peter Jr. Lucky
  female
    Queenie Ms .K
{% endhighlight %}

There we have it! All the data you could possibly want from that hash.