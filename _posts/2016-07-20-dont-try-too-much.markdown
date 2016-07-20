---
layout: post
title:  "Don't try too much"
date:   2016-07-20 21:04:39 +0100
categories: human-factors
comments: true
---

"To try" is an often used expression in the most different contexts. In some, it might make sense and have a useful presence but in other, it might be a dangerous tool.

When one says "I will try", it means that there might be a chance of failure. 
You might say it because you lack the guts to say "no", or the humility to say "I don't know this well enough". Maybe you are afraid to show that you might not be capable of performing some task, or afraid of being looked down.<br>

I understand very well the need of saying "I will try" instead of being transparent. So, I believe there is no point in blaming anyone for taking that way, but it's important to realise that it's not _the way_.

I've learned that using this shield in every situation is not beneficial for anyone: not for yourself, nor your team nor your project.

### A code perspective
It might help if we map this daily-life situation into code.

What's wrong with this?

{% highlight java %}
try {
    final String hello = "hello";
    final String world = "world";
    final String msg = hello + " " + world;
}
catch (Exception e) {
    System.out.println("Exception message...");
}
{% endhighlight %} 

This code has no need to be within a try-catch block: there is no possible failure involved (in a sane world), so it's wrong to do this. 

On the other hand, in this example it makes sense to "try":

{% highlight java %}
try {
    final Optional<String> firstLine= Files.lines(Paths.get("book")).findFirst();
    //do something here
}
catch (IOException e) {
    System.out.println("Got an IOException");
}
{% endhighlight %} 

There is a chance of failure: the book might not exist. So here one must not say "I am going to read the first line of the book" because it depends on external factors, and it's even fundamental that you are aware of it otherwise your system crashes.

Another, less obvious, example of possible failure here is about getting the first line of the book because it might be empty (sweet modern art). In this case, we don't deal with it with a try-catch block but using ```Optional```.

So, there are cases where it's advisable or even fundamental to have a try-catch approach and others cases where it is just wrong to approach the problem that way.

### It's all about Commitment

While I can commit myself to do ```final String msg = hello + " " + world;``` from the first example, I can't commit myself to read the first line of the book, i.e., I must be ready for it to fail.

This kind of honesty and realization that we must all have or develop as programmers, both as individuals and as team members. 

If the book isn't there or if it is empty, no problem, because the code is ready to fail.

The same goes in real-life. 
When one says "I will try", it is detaching commitment and preparing a failure. It is fine when appropriate, but it's a habit not to keep when used otherwise. 
 
### A daily-life perspective 

Imagine a couple getting married and having vows such as:

_"I promise I will try to love you"_ instead of _"I promise I will love you"_. <br>
Do you notice the difference? It's more than words. It's substantially a __different commitment__.<br>
It's not that the second is not aware of the difficulties he might find in the future to love his spouse (or shouldn't), it's about how he declares the meaning of his possible unsuccess as a failure.

If the first one doesn't manage to love his spouse there is no failure in his commitment: he just promised to try.

Or, even, imagine you are planning a drink later on with a friend and you deal to meet at 19h in some place, and your friend replies "Ok, I will try!" without a context. Will you trust you will not end up waiting forever?

### And now what? 

My experience is that commitment that makes us grow.
Therefore, it is important to understand why we are "trying" too much and improve that, commit more and in a more sensible way.

The need to _"try"_ usually comes from:

- __lack of knowledge:__ working in a new environment, not familiar with some tool, etc.
- __lack of experience:__ even when facing a different problem, having experience in something similar one gives you confidence. If you don't have yet that experience, you lack the confidence to be sure you will make it.
- __lack of both:__ you can't distinguish an easy task from a difficult one.
- __fear of disappointing others__


#### ADVISES
From my experience, my bits of advice to improve here are:

    
- __Start small__ <br>
    If you are not sure about what you can accomplish within a project, start by picking the simpler tasks. If you can't make such distinction - which shouldn't happen - be open about your situation and ask some teammate with more experience which task is better for you to start with.

- __Be open__ <br>
    "It's better to be looked down then looked up". Don't fight to keep the expectations high on you. Instead, be open to your team about your difficulties and be open to receive help or guidance. Make it clear for others what they can expect from you. It's better that they don't expect a lot and having you filling those expectations than to have them expecting a lot and not being able to respond to that.

- __Be clear__ <br>
    In spite of just saying "Ok, I will try" say "Ok, I'm not sure about this and that because of this reason, but I will try". It lets everyone know why you are unsure which avoids someone to get surprised if you don't manage in the end.

- __Ring the bell. Often.__ <br>
    Add to the conservation above: "I will give it a try for 1 or 2 hours and if I'm not managing to find the way I will let you know, and we can discuss what I've found or where I am struggling." Don't spend 5 hours trying, with no idea of what to do.


<br>

Read more on this in [The Clean Coder by Robert Martin](http://www.amazon.com/Clean-Coder-Conduct-Professional-Programmers/dp/0137081073/ref=sr_1_3?s=books&ie=UTF8&qid=1461772830&sr=1-3&keywords=clean+code). 

