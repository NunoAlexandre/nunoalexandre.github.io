---
layout: post
title: "The Creative Power of Constraints"
date: 2016-12-08 20:00:39 +0100
categories: ideas concepts programming creativity
comments: true
---

This idea that constraints are something heavy and unwanted, an enemy for the creative, is just not true. Constraints have an enormous creativity power.

I have seen countless examples where constraints were the engine and fuel to creative ideas. 
We hear of people that could only afford a very small house that end up designing their own super space-efficient furniture, others who suffer serious injuries and make something awesome out of it, [and so many more examples](https://blog.bufferapp.com/7-examples-of-how-creative-constraints-can-lead-to-amazing-work).


### Time and Other Resources

When I was doing my bachelor, I always worked part-time. The bachelor itself required us to work our ass off, so now you can imagine that together with a part-time.

<small><em>When I say part-time, I'm not talking about half a day but more like from 5h to 20h a week.</em></small>

During my last year of bachelor I was studying full time and holding two part-time jobs. 

My closest friends in university during a meeting worriedly told me that I would not manage to handle everything. And one of the best students of the course once came to me asking me how could I manage to not fail at any course while working part-time.

<small><em>Believe me, the point of telling all this is not self-promotion, at all.</em></small>

The great lesson I took was that time like other resources is much better spent when there are constraints. 
If you have lots of money, you will spend it. 
If you have three days to do something that would take you one, you will end up taking three days. But if you have only half a day, you will make it in half a day. 

Sure, this is not scientific, but you get the idea, which was the very thing I experienced: Every slot of time I had was precious and I took it to the limit. 

The constraint of time made me much more proactive, efficient and creative in the ways I would approach the work in hands.


### In Software

In terms of how we develop software, there are some constraints that if you hold to them while developing your code, they will bring you to another level of programming, a much more creative, fine and beautiful way. 

I think most of these ideas came up from reading The Clean Code(r) from Uncle Bob.


Uncle Bob in one of his books mentions that we a key idea to have in mind every second while programming is to think _"this piece of code should be somewhere else"_ as to say that it should be encapsulated on its own domain.


This idea is a tiny seed with great fruits. My experience after being "infected" by this idea was that I almost felt paralysed when I had to design and to code. Seriously, I would feel blocked and always unsure. The way I overcame it was by following the short and simple mantra by Joe Amstrong "first make it work, then make it beautiful". I find this the best way to go. It is easy to overthink and overengineer a solution from the very beginning and that blinds you from viewing your code in a raw way and being able to envision better ways to organise it or to rethink it.

#### Methods size
Another, connected, powerful constraint is to forbid yourself from writing methods longer than 3 lines. 

If you are not used to it you will probably act defensively to this idea. That's great, there's space for this seed to grow within you. 

By following this constraint you will inevitably end up with smaller objects, better separation, much more readable code and code that is easier to debug.

#### Class size

Limit your classes to have at most 70 lines of actual code, i.e, not including import statements. 

If we look at the most known Java libraries, frameworks or platforms, most of the classes are truly huge. It's crazy! 

Remember that the S in SOLID means Single-Responsibility. 
Huge classes most of the times, if not always, mean that there is no separation of concerns, it's all in the same bag. If your class(es) are reaching a longer number of lines, reconsider seriously how you could break it into smaller, more concise and concern-specific classes.


#### Assignments
Look for ways of writing your code in a way where you avoid assignments. This is not always the best solution, but if you get in touch with Functional Programming you will find more elegant ways of doing things than to have lines of assignments and state changing.


#### No static methods
Static methods are not an Object-Oriented solution. 

Although I value the voice of purists such as Yegor and David West, I will not repeat their voice in full volume here. 

Static Methods higher the level of coupling, since you are referring a specific class and method, which means that polymorphism is out of option and that you are thinking in a procedural way in the world where objects are the citizens.


#### No Comments
I've [written about this already here](http://nunoalexandre.com/2016/04/29/comments). 

I like, up to an extent, to have class-level comments, which make it friendly to understand quickly what's the scope of the class. But mind that the name should be enough. 

In any case, most of the times comments are a way of compensating the unreadability of the code so challenge yourself: instead of dropping a comment, think deeper on how you can refactor the code in order to make it read fluently while being comment-free.


#### Make every attribute final
Encapsulation is supposed to be one of the great pillars of OO. 

First, encapsulation in OO is not better than encapsulation in procedural languages such as C, and maybe even better. 

Encapsulation in objects is the opposite of what most of the programming world is doing to objects: We set the attributes private but then we add a getter and a setter, breaking the encapsulation entirely. **Avoid this!** 

Make the attributes final and rejoice having immutable objects. 
This might not be 100% practical in every case, but make this your rule and not your exception.

#### No if-else
If-else can be translated to "if a condition matches, run this bunch of code, otherwise run this other bunch of code". 

To say that ternary operator is the same thing is wrong since the ternary operator is a sort of functional programming equivalent of the if-else, so not quite the same thing.

To reason about code full of if-else statements is just painful, and more times than not it is a sign that factual logic is being spread across the code instead of encapsulated. 

### And More
If you are an Object-Oriented programmer, have a look into Functional Programming.

Can you think of developing software without variables, for example? Yes, it is possible, and to learn how to program with that constraint, among others, is an amazing lesson and it will make your creativity exercise and expand.
