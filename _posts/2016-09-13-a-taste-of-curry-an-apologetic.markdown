---
layout: post
title:  "A Taste of Curry - An Apologetic"
date:   2016-09-13 19:30:39 +0100
categories: languages haskell java concepts
comments: true
---

What if your functions would only, actually, take a single argument? 
Currying is a very nice feature that Haskell offers that allows cleaner code to be written and leverages composition.

<blockquote>
Currying is the process of transforming a function that takes multiple arguments into a function that takes just a single argument and returns another function if any arguments are still needed.</blockquote>

Alright, so I have read this exceprt on an answer to the question on Quora: [What programming language do people hate the most, and why?](https://www.quora.com/What-programming-language-do-people-hate-the-most-and-why/answer/Arunav-Sanyal-1)

<blockquote>
    Haskell is too goddamn opinionated on FP. FP is a wonderful paradigm for programming, but most FP enthusiasts champion it as a be all and end all for every task on the planet. And that simply is not true. This is most obvious in the Haskell community. I hate the community more than the language. <strong>That being said, any language that hogties me to prevent me from using basic constructs like loops and multi argument functions <u>is not worth spending even a microsecond on</u>.</strong>
</blockquote>


I had to reply to the block I make bold:

<blockquote>
I see your point about the extreme purist attitude from the Haskell community but this excerpt is just not a good point, at all. Why using “basic constructs” if there are other and better ways of doing it? <u>And you can have functions with multiple arguments, they are just designed in a special way that allows interesting features like currying. And if a language makes you think differently about programming it already is worth spending a microsecond on it.</u>
</blockquote>


To which I got answered:

<blockquote>
<u>I dont believe currying is a better way to do things. Currying is like building stuff with lego blocks. But its asymptotically more unreadable as a result of increasing complexity. f(g(x)) = y is what currying solves, but I expressely want f(x, y) = z as it reads better, makes more sense and does not hogtie me to define two separate methods when I just wanted one piece of functionality that does exactly one thing. Think about a how a curryied impl would look like if you have 50 different things that is happening to the inputs?</u>
<br>
I use scala so i know how to curry stuff, but its way more elegant to use multi var functions when code bases implode in size. Its a restriction that is no palatable to me.
<br>
Same goes for loops. Loops map cognitively to certain tasks that recursion does not (vice versa is also true,but I find loops way more elegant for a vast majority of problems).
</blockquote>


To which I ansered the following, which I make the gross content of this post:


## What I have to say


<blockquote><i>I dont believe currying is a better way to do things. Currying is like building stuff with lego blocks.</i></blockquote>

So I guess you don’t believe that complexity is better built by composing simple parts together?

Your example about “what curry solves” it’s not even a currying example but a function composition example.
Consider for example the sum function:

{% highlight haskell %}
(+) :: Num a => a -> a -> a
{% endhighlight %}

So by currying:

{% highlight haskell %}
1 + 2  == (+1) 2
{% endhighlight %}

And this is useful because you can compose partial applications in a very clean and readable way. 

About your “```f(x, y) = z``` as it reads better”. Does it really? I don’t really see your problem here. Is it about the brackets? you can have exactly the same style in Haskell, you just don’t have the brackets.

For instance:

{% highlight haskell %}
elem :: Eq a => a -> [a] -> Bool
{% endhighlight %}

you can use it like:

{% highlight haskell %}
elem x list
{% endhighlight %}

If you mean that ```elem(x, list)``` reads better, that’s a just an opinion without much more to add, and with arguments against (more characters involved, more messy to look at)

So the conclusion is that currying doesn’t stop you from invoking functions like you do in Scala. It just provides something extra.

An alternative to curry (in such cases) is using anonymous lambdas. 
For the two functions above you can either do:

{% highlight haskell %}
map (+1) list
--or
map (\x -> x+1) list
{% endhighlight %}

and

{% highlight haskell %}
filter (elem 3) listOfLists
--or
filter (\list -> elem 3 list) listOfLists
{% endhighlight %}

I guess you are not really arguing that the second versions are more readable?

And comparing such thing to Java for example:

{% highlight java %}
list.stream().map(x -> x +1).collect(Collectors.toList());
{% endhighlight %}

and

{% highlight java %}
listOfLists.stream().filter(list -> list.contains(3))
   .collect(Collectors.toList());
{% endhighlight %}


Is it even necessary to discuss which style is cleaner, more concise and readable? 

<blockquote>
<i>“Think about a how a curryied impl would look like if you have 50 different things that is happening to the inputs?”</i>
</blockquote>

Ok, again, I’m not sure if you confused currying with function composition.

I hope we both agree that functions should not have more than few arguments, so a case of 50 arguments might not be what you meant but maybe composing 50 functions instead?

Even in such hypothetical case, that would straight away be a case to better breakdown the computation into better smaller blocks, first of all. And, sorry, I just don’t see any strength in your arguments even for such case. Would it be better in an imperative style?

“but I find loops way more elegant for a vast majority of problems”. It happens that I don’t like for or while loops. Now what? Where’s your argument? Loops in general are far from being elegant. In Haskell you have map, filter, fold, etc etc that are way more elegant ways of doing these reductions applications.
Still, I agree that a loop might be a good fit for some problems but why would you be so against other ways of thinking about programming?

It seems that you actually really never spent a microsecond with Haskell.

<blockquote><i>"A language that doesn’t affect the way
you think about programming, is not 
worth knowing."</i> – Alan Perlis
</blockquote>



