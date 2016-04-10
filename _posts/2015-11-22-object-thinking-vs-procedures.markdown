---
layout: post
title:  "Object Thinking – Behaviour vs Procedures"
date:   2015-11-22 16:00:39 +0100
categories: object-thinking
---
Consider we need to work with a list of numbers and find the need to encapsulate some methods to help us out.

The first impulse would be to make something like a static IntegersListUtils. 
Then, if for example we needed to get the maximum number, we would do IntegersListUtils.sort(list).get(0).

Enough is to say no - at all - to utils classes with static methods.
What is this class all about? About knowing and dealing with lists of numbers.

What about having instead a Mathematician object? and the requests would be such as mathematician.maximum(list), mathematician.average(list), etc.

We should of course be careful on such approach as "mathematician" implies a large knowledge and we probably don't want to have one class like that. 

The point to be stressed is to design the code around object-thinking rather than procedures, favouring flexibility, readability and polymorphism. 