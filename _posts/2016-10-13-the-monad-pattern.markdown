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

I was firstly introduced to Monads in Haskell, where they are vastly used.

I was enjoying the language a great deal... and then Monads showed up. 
_Every good thing comes to an end_, I thought.

I was wrong.

When I went back to learn Haskell more seriously, I was curious to face Monads again.
I was very eager to understand them as I thought that would mean I had some special skill and I started creating a really messy image of it in my mind. I had a lot of not connected words to explain it but I was starting to get a _feeling_ how what they were. 

### Key Thoughts about Monads

These are some key thoughts to have or that you will read about Monads:

```Computation builder``` ```Containers``` ```Monoids``` ```Context``` ```Cathegory Theory``` ```Wrapper``` ```Bound computation ```

Wikipedia says:

<blockquote>In functional programming, monads are a way to build computer programs by joining simple components in predictable and robust ways.</blockquote>

I would remove the _functional_ bit and would add that they are a design pattern. 

Monads leverage ```DRY```ness and make contextualized computation explicit.


## So what is a Monad

What is a Song? We can think of a Song as something that you can play that produces music: 

{% highlight java %}
public interface Song {
    Music play();
}
{% endhighlight %}

So this means that now anything that you can play() that produces Music is a song. 
This might not be true in real life but in this universe, we are building it is.

As a ```Song``` is something that _can be played__, producing ```Music```, a Monad is something that can be governed by two (monadic) laws: ```unit``` and ```bind```. 

The Monad Class declaration of these laws in Haskell gives us a pretty neat view of what's involved:

{% highlight haskell %}
class  Monad m  where
   (>>=)            :: m a -> (a -> m b) -> m b
   return           :: a -> m a
{% endhighlight %}


- ```return``` gets a value ```a``` and returns a ```Monad``` of type ```a```. So it wraps the value into a Monad. 
- ```bind``` (aka ```>>=```) gets two arguments: 1) a Monad of type a (```m a```), and 2) a function that receives a value of type ```a``` and returns a Monad of type ```b``` and finally returns a Monad of value b.


### The Clock Monad

In the video below by Brian Beckman he gives this example that I found simple and precise.

Think of a clock, hours-wise. It is made of numbers from 1 to 12. 
Now think of these 2 laws above. 

How would you build a Clock Monad?

The ```return``` function could be defined as a function that receives any integral number and puts it into this 1 to 12 numbers context:

{% highlight haskell %}
return x = mod x 12 + 1
{% endhighlight %}

The ```bind``` function here would get a Clock Hour number and a function that work with such numbers and 'return' the result of applying the hour to that function.

This is a very stripped example but it helped me to get the idea.


## Maybe Monad?

A very known Monad is the ```Maybe``` Monad. (Optional in Java, Option in Scala,...)

It is defined as follows in Haskell:

{% highlight haskell %}
data Maybe a = Just a | Nothing
{% endhighlight %}

And the Monadic laws for the ```Maybe``` Monad are implemented as follows:

{% highlight haskell %}
return :: a -> Maybe a
return x = Just x

(>>=)  :: Maybe a -> (a -> Maybe b) -> Maybe b
(>>=) Nothing  g = Nothing
(>>=) (Just x) g = g x
{% endhighlight %}


This next example of presented in the [Understanding Monads](https://en.wikibooks.org/wiki/Haskell/Understanding_monads) guide.


### Family Tree with the Maybe Monad

To build a family tree - from the 20th century - we need to find the mother or/and the father of a concrete Person.
But it's not certain that the mother or the father of a person X will be found, so it's a Maybe.

{% highlight haskell %}
father :: Person -> Maybe Person
mother :: Person -> Maybe Person
{% endhighlight %}


With the Maybe Monad we can find the maternal grandfather like this:

{% highlight haskell %}
maternalGrandfather p = do
   case mother p of
      Nothing -> Nothing
      Just psMother -> father psMother
{% endhighlight %}

Fair enough. But using the monadic power this can be written as:

{% highlight haskell %}
maternalGrandfather = (mother >>= father)
{% endhighlight %}

Very clean, super expressive and no need for dirty step by step assignments to feed the next computation.


This gives a tiny clue of how Monads can be great for building complex computation with very simple parts, which is precisely how complexity is better built and maintainable. 


## More Resources about Monads

### Monads To The Rescue 

While following the online course [Functional Programming in Haskell @ FutureLearn](https://www.futurelearn.com/courses/functional-programming-haskell/), I've got to know about [Katie Miller](http://www.codemiller.com) and her beautiful talk about Monads.

So far, this has been the best talk about Monads so it's my first suggestion if you want to get into it. Katie also explains it brilliantly through Java code and way of thinking.

<iframe width="560" height="315" src="https://www.youtube.com/embed/MlZCiiKGbb0" frameborder="0" allowfullscreen></iframe>


### Don't Fear the Monad

Next to this, I also enjoyed a great deal to watch this explanation from [Brian Beckman](https://www.linkedin.com/in/brianbeckman). Brian kept my attention by his efforts in making the object of his explanation look simple and to avoid the overwhelming feeling win.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZhuHCtR3xq8" frameborder="0" allowfullscreen></iframe>