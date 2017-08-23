---
layout: post
title:  Understanding composition
date:   2017-08-23 17:50:39 +02
categories: haskell composition til
comments: true
---

_Function composition is the pointwise application of one function to the result of another to produce a third function._, [says Wikipedia](https://en.wikipedia.org/wiki/Function_composition). Here I show what I was getting wrong about it.


Having the two functions:

{% highlight haskell %}
f :: B -> C
g :: A -> B
{% endhighlight %}

We can compose them as `(f . g)`, since `f`'s input type is `g`'s output type.

### Example

Say we have:

{% highlight haskell %}
f :: Int -> Bool
f = (/=0)

g :: Char -> Int
g 'y' = 1
g _   = 0
{% endhighlight %}

Then we can compose them as:

{% highlight haskell %}
> (f . g) 'y'
True
> (f . g) 'n'
> False
{% endhighlight %}

### My mistake

I knew that composition was lazy:

{% highlight haskell %}
> (filter even) . (filter (>4)) $ [1..20]
[6,8,10,12,14,16,18,20]
{% endhighlight %}

But I missed how composition really works.

See, I always thought that the right side of the composition (in the example above, the function `g`) was the first to be computed, but that is wrong.

It turns out, as it makes sense to be, that `f` is the first function to be computed. This may sound odd to you as if `f` expects `Char`. But that's obviously not the case.

Remember that Haskell is lazy. In a strict language, the composition would most likely happen as I was expecting it to happen but not in a lazy language.

Note that `g(x)` is the value that `f` receives, thus `g` cannot be evaluated before (entering) `f` as that would mean strict evaluation of arguments.

### Proof  

Let's show that the left most function is the first to be computed.

Having these definitions for `f` and `g`:

{% highlight haskell %}
f :: Int -> Bool
f = error "failed in f"

g :: Char -> Int
g = error "failed in g"
{% endhighlight %}

An application based on their composition results in `*** Exception: failed in f`:

{% highlight haskell %}
> (f . g) $ 'y'
*** Exception: failed in f
{% endhighlight %}


### Usefulness

One quick example, out of infinite possible ones:

When building [`Wai.Application`](https://hackage.haskell.org/package/wai) middlewares, the order in which you process a request does matter.

For instance, you want to first ensure the request is not targeting some protected endpoint before authenticating it, so to save unnecessary computation in a positive case.

In a strict language, handling such dependency while being able to compose wouldn't be easy and clean.
In Haskell, however, you simply need to set the order of the composition so that it matches your business logic, without having each middleware knowing about each other and falling into a dependency hell.

Simple but fundamental and useful piece of knowledge to keep in mind.
