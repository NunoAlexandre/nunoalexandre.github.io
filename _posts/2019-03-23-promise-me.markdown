---
layout: post
title:  "Promise me"
date:   2019-03-23 20:11:39 +0100
categories: swift clean-code composability refactoring promises callbacks
comments: true
---

Refactoring is perhaps the most underappreciated task in software development. For me,
it can be great fun and joyful to transform inedible code into an elegant & maintainable code.
In this blog post, I quickly share a small part of a significant refactoring I just completed, and how moving from callbacks to promises helped design a declarative and composable download pipeline.

_Disclaimer: This blog post is **not** promoting Promises per se. For what it's worth,
I share [this opinion](https://www.reddit.com/r/haskell/comments/sijsg/what_is_the_haskell_equivalent_to_javascript/c4eclqx?utm_source=share&utm_medium=web2x) on the matter._

## The Problem

The project in question here is the Kidiyo iOS app. Kidiyo believes in introducing
digital media to young children through constructive, funny, and healthy content.
Therefore, we stand to be the one-in-all platform with all sorts of different items, such as games, videos, ebooks, and offscreen connections.

Being a platform means that the items we provide must be made available and improved dynamically, without the need for new app releases. Currently, those items are downloaded to be played, expect for streamable videos and music.

In the new version of Kidiyo, soon to be released, I had three requirements for
the downloads:

1. The download pipeline should be declarative so that a non-techie can quickly grasp what it does
2. Downloading items from different sources should be abstracted and unified
3. The app should be able to pick up on an ongoing download after submitting it

This post focused mostly on the first requirement, followed by the second. The third is
subject to a new post.


### Starting point

The app supported downloading items from two different sources.

The snippet below is an exact copy of one of the methods responsible for downloading items from one of the sources, in this case from Firebase. The method responsible for downloading items from the other source was remarkably identical in structure, differing in details on how those items were unpacked and stored locally.

<script src="https://gist.github.com/NunoAlexandre/85a0b73410fdb44e776bc6a5ea58042b.js"></script>

There are many things wrong here:

* It's imperative
* It's a big callback hell
* It's not composable nor sanely extendable
* It has something like 9 levels of nesting
* It's a dump of responsibilities from different levels of abstraction
* The success and the error flows are far away, therefore harder to visualise

### Refactoring

Functional principles to the rescue. The way was to break down all the different
sub-tasks taking place all together into separate, self-sufficient units that always
returned either a _token_ for a next step or an Error. Very similar to `Either`,
but with the requirement to be run _async_. That way, I could design the download
pipeline declaratively, where each part of the chain can be put it an out effortlessly.

The result is the following:

<script src="https://gist.github.com/NunoAlexandre/a85f3200a78b2e95919c730a507d0e1c.js"></script>


**Note:** I use the [freshOS/then](https://github.com/freshOS/then) Promises Library for Swift with Async/Await.
