---
layout: post
title:  Happy Haskell developer
date:   2017-07-13 19:50:39 +02
categories: haskell experience channable
comments: true
---

In the beginning of June this year I joined [Channable](https://channable.com) as a backend developer. After being tired of Java, I decided to dream big and look for jobs in Haskell in The Netherlands and I found this super cool successful startup. Today I will tell how's been my experience with Haskell so far.

If you don't know [why I care about Haskell](http://nunoalexandre.com/2017/06/11/why-i-care-about-haskell), you cannot imagine the little happy and giggling child that is inside me about this new job. I was a Haskell lover and evangelist within the Java world I was in before, but I never thought I would ever work with Haskell _for real_.

Fortunately, I was wrong and now I do have the privilege of working primarily with Haskell at [Channable](https://channable.com), next to Python and Scala.

These are the topics I will cover about my experience so far:

* Tooling
* Documentation
* Community
* Practicality


### Tooling

By _tooling_ I mean mostly IDEs and build tools.

In [my talk on Haskell](https://www.youtube.com/embed/pjIw0-xPoZ0?ecver=2), I mentioned that tooling was a downside for Haskell. My view, after production experience, is different.
If we compare almost any language tooling to Java's, we will probably fall short. Java is the number one in the industry, therefore the tooling goes after the needs.

I have been used Atom with [this setup](https://github.com/simonmichael/haskell-atom-setup) and it works like a charm. It provides all sorts of things I need and most developers need.

About build tools, I mentioned that once I tried setting up a few Haskell web frameworks projects and that it took forever just to get it to build. And it's true. But it's only for the first time, as that's usually when it needs to fetch the most dependencies at once and compile them.

At Channable, we use `Stack` and it just works. I found it always easy to add or change some configuration and it always worked as I expected.  

The bit I don't like as much is Hackage, is the Haskell libraries gateway. It starts with the lack of aesthetics, although it's not often much better elsewhere. The main problem I found is that I rarely know where to look at when checking a library page.

For instance, if you want to grab the library package name to add to your dependencies list, good luck! The package name is the first part of the top left corner and the latest version is the last one of the list, in the middle of the page. Once you know this it is fine, but how hard would to have the package name and latest version in a central position?

In conclusion, the tooling is pretty acceptable and sufficient.

### Documentation
As I said above, I find hackage in need of a serious redesign. I like to read the beautiful and helpful documentation, such as we find for Elixir.

One really nice thing about Haskell documentation is that from a library API page you easily access the source code. That is really nice. In Scala and Python many times you just get a javadocs of a method that doesn't really satisfy your needs and there is no O(1) access to the source code.

I would like to see some linking between hackage and GitHub though. One thing I noticed is that often enough people authors of some library are better with computers than with people, and the documentation they provide goes close to nothing: The summary is poor and no examples or only very simple ones are provided, requiring you to go through the tests or the source code to figure it out yourself.

The good part is that since Haskell is strongly and statically typed, you can easily get the answers you are looking for by looking at the code. I also find the comments in the Haskell world pretty good.

In conclusion, I don't find Haskell documentation great but I don't find it worse than many other. Maybe my fussiness about UI can gather some people in a movement to prettify hackage.


### Community

Before joining Channable, I never really talked with people that knew advanced Haskell and/or that used it for real applications. Thus I was curious about how they would look like.

I wondered if Haskell developers had conversations about Monoids, Endofunctors, Arrows, etc. Well, _they do_! And it's pretty nice, even though it can be highly overwhelming.

The Haskell developers I work with and others I met, share a deep enthusiasm, and I haven't spotted a sense of arrogance in any, despite what the world says.

My questions and lack of knowledge on some topic were always welcomed and friendly addressed.

Also, I had a nice beer with [Stephen Diehl](https://github.com/sdiehl), a big name, together with some teammates of mine. Stephen was super reachable and nice to talk with. I like this, I like small small communities, where you can easily reach everyone, no matter how known and respected they are.

Also, most Haskell developers I met are not just into Haskell. They all have a broad interest in programming languages, they are not stale with a single language they think that fits every single problem.

This is a big improvement from my experience in the Java world, where there is the feeling that nothing else besides Java is mature and worth the time.


### Practicality
This is the most heard one: _Haskell is not practical_. This is plain wrong.

I was afraid that I wouldn't like to use Haskell at work as much as I did to play with it in my free time, but the opposite is happening.

I have experienced true joy while working with Haskell. Do you what it is to have a smile while working? That's what I have been experiencing.

It feels awesome to work daily with a beautiful and robust language. I find it much simpler to program in a functional style, and I love how testable every piece of code I write is.
You see, without (aka controlled) side-effects, testing becomes trivial.

The praise you hear about the type-system is not for nothing: it is awesome indeed and leads to correct and rock solid software.

Every released piece that I have worked have not let me down so far. When it compiles, and the tests are green, I trust it. And that is priceless.
