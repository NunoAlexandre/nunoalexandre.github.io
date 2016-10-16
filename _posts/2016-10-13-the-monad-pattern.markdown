---
layout: post
title:  "The Monad Pattern"
date:   2016-10-13 20:20:39 +0100
categories: languages Haskell java concepts patterns
comments: true
---


Monads were not the easiest concept for me to fully understand. 
There are endless QAs and tutorials about Monads and each one adds something more. 
And then I released that Monad is actually a design pattern and it all made sense.

## To Learn About Monads

These two videos below are great resources to understand what Monads are and why they are useful.
Although I'm going to add my breadcrumb on this more below, I might not be of a better help than to listen to these talks.

### Monads To The Rescue

While following the online course [Functional Programming in Haskell @ FutureLearn](https://www.futurelearn.com/courses/functional-programming-haskell/), I've got to know about [Katie Miller](http://www.codemiller.com) and her great talk about Monads.

So far, this has been the best talk about Monads I listen to, so it's my first suggestion if you want to get into it. Katie also explains it brilliantly with Java code and way of thinking.

<iframe width="560" height="315" src="https://www.youtube.com/embed/MlZCiiKGbb0" frameborder="0" allowfullscreen></iframe>


### Don't Fear the Monad

I also enjoyed a great deal to watch this explanation from [Brian Beckman](https://www.linkedin.com/in/brianbeckman). Brian kept my attention by his efforts in making the object of his explanation look simple and to avoid the overwhelming feeling win.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZhuHCtR3xq8" frameborder="0" allowfullscreen></iframe>


## Me and the Monad

I was firstly introduced to Monads during a University course on Haskell, where they are vastly used.

I was enjoying the language a great deal... and then Monads showed up. <br>
_Every good thing comes to an end_, I thought.

I was wrong.

When I went back to learn Haskell more seriously, I was curious to face Monads again.
I was very eager to understand them as I thought that would mean I had some special skill and I started creating a really messy image of it in my mind. 
I had a lot of unrelated ways to explain it but slowly it started getting clearer and less scary. 

### Getting an idea 

Wikipedia says:

<blockquote>In functional programming, monads are a way to build computer programs by joining simple components in predictable and robust ways.</blockquote>

So, this is interesting as it brings some key points. 
A Monad is a design pattern that consists of allowing complex computation to be built by composing very simple components in an explicit and controlled manner. Some say that Monads are **_the_** way of achieving it.


I like to think of Monad as a container:

1. Each Monad type is a special type of container
2. that defines a specific rule for interaction,
3. And each type of container holds values of any type.

The _rule_ defines that every interaction with the container is passed to and is handled by and within the container and its output never come out of the container.

<small>
[ If you know about [Docker](http://docker.com) containers this behaviour might sound familiar ]
</small>

If we were to stop here, we would actually be talking about [Monoids](https://www.quora.com/What-is-the-difference-between-monoid-and-monad).

#### A simple example

From what has been said above, a simple example to visualize these concepts is to think of a clock.

A clock has hours, which are a collection of numbers from 0 to 12.
If we think a clock as such a container, the interaction rule to interact with the hours of a clock is that at the end of the interaction, the hours of the clock remain within the 0..12 range. (mod 12)


### So what is actually a Monad

The Monad Class declaration, in Haskell, gives us a pretty neat high-level view of what's involved:

{% highlight haskell %}
class  Monad m  where
   return           :: a -> m a
   (>>=)            :: m a -> (a -> m b) -> m b
{% endhighlight %}

Think of the lower ```m``` has a specific type of container, and of ```a``` and ```b``` has data types.

Having that in mind, what we have is that for something to be a Monad, ```m```, it must define the following __two operations__ as they are intended:

__First - ```return```__ (nothing to do with the _return_ keyword you are used to)


```return```, also called ```unit```, states that given a value of type ```a```, that value is placed within the specific container ```m```. 

Imagine the Monad being a plate. The ```return``` function-to-be defines that given any kind of food, it will return a plate with that piece of food in it (called the _minimal context_).

__Second - ```(>>=)```__ (similar to a shell pipe)


- ```(>>=)```, also called ```bind```, as the role of binding computation. This is where the idea of building complexity using simple blocks takes place.

This function-to-be defines that given gives two arguments, outputs a container of the same type holding a value of any type (maybe the same, ```a```, or not).

Now, those two arguments are:

   1. A Monad ```m a```, this is, a specific type of container holding a value of type ```a```.
   2. A function that receives a value of that same type ```a``` and returns a container of the same type received in the first argument, holding a value of any type (maybe the same, ```a```, or not).

But it's not over yet. <br>
This two rules must be enough to fullfill the following __three (monadic) laws__:

* __Left identity__:   ```return a >>= f```  __≡__  ```f a```<br>
* __Right identity__:  ```m >>= return```  __≡__  ```m```<br>
* __Associativity__: ```(m >>= f) >>= g```  __≡__  ```m >>= (\x -> f x >>= g)```<br>


And that's it. It might be scary at first, but it's actually simple and it's great to be able to think of code in terms of mathematics.


## Maybe Monad

A very known Monad is the ```Maybe``` Monad. (Optional in Java, Option in Scala, etc)

It is defined as follows in Haskell:

{% highlight haskell %}
data Maybe a = Just a | Nothing
{% endhighlight %}

And the Monadic rules for the ```Maybe``` Monad are implemented as follows:

{% highlight haskell %}
return :: a -> Maybe a
return x = Just x

(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
(>>=) Nothing  g = Nothing
(>>=) (Just x) g = g x
{% endhighlight %}


### Building a Family Tree with the Maybe Monad

This next example is presented in the [Understanding Monads](https://en.wikibooks.org/wiki/Haskell/Understanding_monads) guide.

To build a family tree - from the 20th century - we need to find the mother or/and the father of a concrete Person.
But it's not certain that the mother or the father of a person X will be found, so it's a Maybe.

{% highlight haskell %}
father :: Person -> Maybe Person
mother :: Person -> Maybe Person
{% endhighlight %}


With the Maybe Monad we can find the maternal grandfather like this:

{% highlight haskell %}
maternalGrandfather :: Person -> Maybe Person
maternalGrandfather p = do
   case mother p of
      Nothing -> Nothing
      Just psMother -> father psMother
{% endhighlight %}


Fair enough.<br> 
But using the monadic pure syntax this can be written as:

{% highlight haskell %}
maternalGrandfather :: Person -> Maybe Person
maternalGrandfather = (mother >>= father)
{% endhighlight %}

Very clean, super expressive and no need for dirty step by step process with assignments and if-else's or switch cases.


This gives a tiny clue of how powerful Monads are for building complex computation with very simple parts in a controlled manner, which is precisely how complexity is better built and maintainable. 
