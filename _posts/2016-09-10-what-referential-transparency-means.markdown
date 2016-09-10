---
layout: post
title:  "What Referential Transparency means"
date:   2016-09-10 18:20:39 +0100
categories: languages haskell java concepts
comments: true
---


When I think of referential transparency, I think that one reference to something is transparent, this is, there are no hidden parts involved.

In Haskell, a function only acts upon the arguments that were passed. It’s not possible that within a function you refer to something outside the function scope.

## A referential transparency example

In linguistic terms, you can think of referential transparency like this:

“I am in the capital of Portugal” being the same than “I am in Lisbon”.

This means that you can simply replace a function reference by its value while keeping the behaviour the same. For example, if you have a function such as:


{% highlight haskell %}
plusThree :: Num a => a -> a
plusThree = (+3)
{% endhighlight %}

Just liked I could replace ‘capital of Portugal’ with’ Lisbon’, I can either apply ‘plusThree’ (reference, maps to ‘capital of Portugal’ ) or the ‘value’ of that function, i.e, ‘(+3)’ (maps to ‘Lisbon’):


{% highlight haskell %}
> plusThree 4 == (+3) 4
True
{% endhighlight %}



## Example without referential transparency


{% highlight java %}
//global variable whose value might change during execution 
int x;
(…)

int plusX(int y) {
 return x + y;
}
{% endhighlight %}

In such case you can not, theoretically, replace the method reference by its value, not only by syntactic issues but because plusX holds external references (that might change, who knows?), breaking referential transparency.


If you, by chance, are not understanding the different between referential transparency and purity in Haskell context:

For a function to be pure, it needs to be referentially transparent and have no side effects.

(This is an adapted version of my answer on [this question in Quora](https://www.quora.com/What-is-the-significance-of-referential-transparency-in-Haskell/answer/Nuno-Alexandre-4))