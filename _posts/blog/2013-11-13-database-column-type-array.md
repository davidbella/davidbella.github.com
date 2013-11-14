---
layout: post
title: "Database Column Type - Array"
description: ""
category: "blog"
tags: ["Visualization", "Databases"]
---

**Okay, So More Like "List"**  
Relational databases are a great way to store, well, related data, in a base. Often times, we have to store items using a "has many" type of relationship. This post will talk about about what it means to "have many" and how it relates a bit to how arrays work in a standard programming environment.

**A Simple Example**  
The classic example for database relations in a web development context is that posts can have many comments. Let's imagine a very simple posts table in our database:

![Naive Posts Table]({{ site.url }}/assets/blog/db-list-post.png)

This doesn't look so bad, but there is one major problem. We need to store a list of comments on this post. Currently, we scale the comments on each post horizontally, adding a new column for each comment that a post has. Creating a new column on a database table dynamically would be very difficult and not very efficient! What if one post had hundreds of comments and another had just one? There would be hundreds of empty columns for that latter row, taking up unnecessary space in the database.

We need some way to represent a list data type in a column. While it is possible to store a comma delimited list of values in a single column to act like an array, this isn't really in the spirit of having normalized relational data in our database. We would then have to rely on an external source (our application, our API, etc.) convert this string into an actual array.

**Tables Are the New Lists**  
Instead, we can *transpose* our data from a *horizontal* array to a *vertical* array. We can do this easily in database speak by creating a new table. Each row in a database table is basically an entry in the array.

![Transpose 1]({{ site.url }}/assets/blog/db-list-pose1.png)
![Transpose 2]({{ site.url }}/assets/blog/db-list-pose2.png)

Now we can simplify the table structure of post and we are left with the much simpler looking:

![Simpler, But Not Done]({{ site.url }}/assets/blog/db-list-post2.png)

What about if we have more than one post though? How do we determine which post each comment belongs to? Luckily, each post is uniquely identified by a primary key, so we can copy this value over as well, and set it to be the foreign key of each comment as appropriate:

![Foreign Key 1]({{ site.url }}/assets/blog/db-list-fk1.png)
![Foreign Key 2]({{ site.url }}/assets/blog/db-list-fk2.png)

Now we have a list of all the comments, and each comment entry can tell us which post it belongs to.

![Final Comments and Posts]({{ site.url }}/assets/blog/db-list-final.png)

Notice that the posts have no concept as to what their comments could be. We have to use the database to get all the comments that link up to the post in question. The next section talks about this a bit.

**Traditional Arrays vs. "Database Lists"**  
We can see visually here how a "list" in a database differs from an "array" in a programming language. Normally we can just index on an array like so:

{% highlight ruby %}
post.comment[0] # to get the first comment
post.comment[2] # to get the third comment
{% endhighlight %}

With this kind of "database list" we have to sort of work backwards and see if the comment we are looking at matches up to the post we are looking at.

{% highlight ruby %}
comment.post == post # is the current comment's post the post we care about?
{% endhighlight %}

In order to get all the comments, we will actually have to run something like this:

"get all comments such that the comment's post id is equal to the post id we are looking for"

{% highlight ruby %}
Comments.all.collect do |comment|
  comment.post_id == post.id
end
{% endhighlight %}

Also, huge shoutout here to http://www.gliffy.com - I have been looking for a simple but effective online diagramming tool for a long time and this site was a joy to work with.