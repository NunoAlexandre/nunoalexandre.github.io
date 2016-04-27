---
layout: post
title:  "The Command Pattern"
date:   2016-02-21 20:00:39 +0100
categories: design-patterns
comments: true
---

I’ve used this pattern in a iOS project where a web page was to be loaded and the native app should handle javascript events. I encapsulated the "web page" into an EmbeddedWebApp object where the javascript<>native bridge was set. I had a factory class (client) where were defined the handled events/commands and their action upon the native side of the app. One of the events (command) was to request a camera capture, that needed to delegate the request to another object (receiver). Note the _Single Responsibility_ principle and how important it is.

<img src="/images/command-pattern.png"/>

###### Image credits belong  to Heads First Design Patterns author’s rights. 