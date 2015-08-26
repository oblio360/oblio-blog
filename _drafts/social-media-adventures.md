--- 
layout: post 
title: "Welcome to Hearthstone, you suck!" 
---

At the very start of this project I decided that I'd follow these basic principles:

* have fun!

* reach out to like-minded people while having fun

If the first part does not rely on anyone except myself, for the second one I depend on a lot of other people, groups of
people and companies.

I figured out that the preferred way to promote something today is via social media networks, and this was the first
step where things got messy. And here is where the current article starts.

So, I got a Github account, created a new organization for this project (Oblio360), created some repos and published
them. So far so good, things went rather smoothly.

Then I decided that I'd promote my articles on Facebook, Google+ and Twitter. And I decided early on that I wouldn't do
it manually, since it is annoying and error prone. So I started digging around to see how that's done.

Unfortunately, just as I expected, each network has its own way of doing things:

* [Google+ Domains](https://developers.google.com/+/domains/getting-started)
* [Facebook Graph API](https://developers.facebook.com/docs/graph-api/using-graph-api/v2.4)
* [Twitter REST API](https://dev.twitter.com/rest/public)

And of course there's also the [OpenSocial standard](http://www.w3.org/Social/WG), which supposedly should cover
everything I need. But as far as I can see it's not really implemented by anyone. The typical problem:

![Xkcd standards](http://imgs.xkcd.com/comics/standards.png)

I might be wrong about actual social networks implementing OpenSocial, so please reply in the comments if I missed
something.

Anyway, standard or no standard, I just wanted to post a message to:

* Facebook
* Google+
* Twitter

without having to do it manually since it's annoying, repetitive and error prone.

Before starting I decided to do a little research to see if something small and practical already exists and I found
some already existing solutions, but I frankly did not feel comfortable with giving my credentials to various apps or
SaaS solutions. I also decided that now would be a good time to follow principle #1: "Have fun!", and write a small tool
that does what I want and publish it as a small Open Source project in case other people want to use it.

So first of all, I defined my problem:

    As a content publisher I want that when a new article is published on my site summaries are automatically published
    on the selected social media network (the initial selection would be Facebook, Twitter and Google+).

    As a developer for this solution I would want to have a tool which can be launched from the command line or a
    command scheduler such as cron and which monitors the source of the articles and does the actual publishing when it
    finds new content.

Based on this definition and my current experience I decided to create a Python 3 utility to do this
