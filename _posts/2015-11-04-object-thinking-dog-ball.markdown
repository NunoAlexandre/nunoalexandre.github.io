---
layout: post
title:  "Object Thinking – A Ball and A Dog"
date:   2015-11-04 19:00:39 +0100
categories: object-thinking
---
>A Ball and A Dog
>In true object-oriented programming, objects are living creatures, like you and me. They are living organisms, with their own behavior, properties and a life cycle.

>Can a living organism have a setter? Can you “set” a ball to a dog? Not really. But that is exactly what the following piece of software is doing:

{% highlight java %}
Dog dog = new Dog();
dog.setBall(new Ball());
{% endhighlight %}

>How does that sound?
>Can you get a ball from a dog? Well, you probably can, if she ate it and you’re doing surgery. In that case, yes, we can “get” a ball from a dog.

>This is what I’m talking about:

{% highlight java %}
Dog dog = new Dog();
Ball ball = dog.getBall();
{% endhighlight %}

> Or an even more ridiculous example:
{% highlight java %}
Dog dog = new Dog();
dog.setWeight(“23kg”);
{% endhighlight %}

>Can you imagine this transaction in the real world? :)
>Does it look similar to what you’re writing every day? If yes, then you’re a procedural programmer. Admit it. And this is what David West has to say about it, on page 30 of his book:
>Step one in the transformation of a successful procedural developer into a successful object developer is a lobotomy.
>Do you need a lobotomy? Well, I definitely needed one and received it, while reading West’s Object Thinking.

>**Object Thinking**
>Start thinking like an object and you will immediately rename those methods. This is what you will probably get:

{% highlight java %}
Dog dog = new Dog();
dog.take(new Ball());
Ball ball = dog.give();
{% endhighlight %}

>Now, we’re treating the dog as a real animal, who can take a ball from us and can give it back, when we ask. Worth mentioning is that the dog can’t give NULL back. Dogs simply don’t know what NULL is :) Object thinking immediately eliminates NULL references from your code.


__From http://www.yegor256.com/2014/09/16/getters-and-setters-are-evil.html__



Definitely I’m for Object Thinking in spite of creating objects as simple data holders or data structures with specific custom assessores.
Still, this example, in my way of seeing this, fails in these points:

1.Object Thinking should get you to design the object differently, what probably means to design a different way of interacting with it. But what we see here is, **no more no less, than just changing methods names**.

2. By changing the names of the method as it was done here, we lost readability: 
  * __dog.take__(new Ball());     – take? take from once place to the other, take like grab it, take like what?
  * __dog.give()__ 	 – give what? Is the dog limited to just give balls? or will he just give his paw? maybe a kiss?



**At last**: 

> They are living organisms, with their own behavior, properties and a life cycle.

First, this doesn’t really apply completely to reality. For instance we might have an Object ‘Stone’ and it’s not a living organism and has no self behaviour.

Getting back the example of dog.setWeight(“23kg”); I agree it sounds weird that an external entity sets instantly a certain weight to a dog. In real life it get’s weight by eating more than the energy it burns or by getting muscle. But should we design an object that changes it self on his own decision? Maybe yes, such as having dog.feedWith( new Chicken() ), and the dog may have an attribute ‘spentCalories’, and my playing with his the chicken.calories() and the its spentCalories, gets to update his weight. Maybe we would need to create a dog metabolic system object…

