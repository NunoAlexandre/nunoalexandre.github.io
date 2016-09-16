---
layout: post
title:  "Love the Lambda"
date:   2016-09-15 19:46:39 +0100
categories: languages Haskell java concepts lambda
comments: true
---


One of the main words to retain about lambda expressions is __anonymous__. A lambda expression is, basically, an anonymous function. 
Lambdas Expressions are a normal feature in functional languages but just few time ago arrived into the Java world. Better late than never though!

## Why is it important?
If you are like me, you will be pleased with writing as less code as possible without losing expressiveness. And that's one of the great advantages of lambdas.

It allows you to write shorter, more objective, more concise and more expressive code. And more function, which is cool!

If you are familiar with [Anynomous classes](https://www.google.pt/search?client=safari&rls=en&q=anynomous+classes&ie=UTF-8&oe=UTF-8&gfe_rd=cr&ei=FQTbV5ShFajA8geOw7-YBQ), lambdas will feel like a breeze to you. 
If you are not, no worries.

## How to interpret them

We tend to feel overwhelmed when looking at code with some uncommon syntax, but this is very simple, __really__.

Let's first look into a simple method in Java:

{% highlight java %}
String hello(String name) {
    return "Hello " + name;
}
{% endhighlight %}

From this, we read that we have this function named __hello__ that receives a String argument (tagged _name_) and it returns a String by prepending "Hello " to the argument.

Notice the amount of code to do this. 

Lambda expressions look like this:

{% highlight java %}
argument -> function code
{% endhighlight %}

Quite simple, right?

So the function above, as a lambda expression, looks like this:

{% highlight java %}
name -> "Hello " + name
{% endhighlight %}


Pretty, right?


## How to apply them (not yet)

From the example above, you might be wondering how you can actually use that lambda in order to produce something. 

That is one interesting point to discuss and uncover. 

Before that (believe me, it will be helpful), let me show you how lambda expressions are used a functional language like Haskell.

In Haskell, the function equivalent to the method above looks like this:

{% highlight haskell %}
hello :: String -> String
hello = ("Hello " ++)
--or
hello x = "Hello " ++ x 
{% endhighlight %}

And the equivalent lambda expression to this is:

{% highlight haskell %}
\x -> "Hello " ++ x
{% endhighlight %}


The point here is that I can apply both in the same way.

{% highlight haskell %}
ghci> hello "world"
"Hello world"

ghci> (\x -> "Hello " ++ x) "world"
"Hello world"
{% endhighlight %}


__In Java, this is not the case.__

## How to use lambdas in Java

I brought up the Anonymous Classes above because Lambda expressions have very much the same purpose.

Oracle's introductory paragraph [about lambda expressions](http://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) states the following:

<blockquote>
    One issue with anonymous classes is that if the implementation of your anonymous class is very simple, such as an interface that contains only one method, then the syntax of anonymous classes may seem unwieldy and unclear. In these cases, <u>you're usually trying to pass functionality as an argument to another method</u>, such as what action should be taken when someone clicks a button. <u>Lambda expressions enable you to do this</u>, to treat functionality as method argument, or code as data.
</blockquote>

The key idea is **_trying to pass functionality as an argument_**.

Before, this was eased through the use of Anonymous classes.


## A Practical Example

Consider the following example where:

* you are accessing a web service
* the methods available to fetch data from it might throw an exception
* you want to map it to Optional<T> instead of having to deal with [nulls and exceptions](http://nunoalexandre.github.io/2016/08/31/why-optional-is-better-than-null) around your system.


__1. I start by__ creating this [functional interface](http://stackoverflow.com/questions/36881826/what-is-use-of-functional-interface-in-java-8/36882003):

{% highlight java %}
@FunctionalInterface
public interface FallibleAction<T> {
    T result() throws Exception;
}
{% endhighlight %}

In other words, a FallibleAction is an Object with a single method, _result_, that returns something of type T that might throw an Exception.


__2. Then I create__ the mapping / adapter abstraction from the outside world os nulls and exceptions into the inside world of Optional:

{% highlight java %}
public class Try {
    public static <T> Optional<T> toGet(FallibleAction<T> action) {
        try {
            return Optional.ofNullable(action.result());
        } catch (Exception e) {
            return Optional.empty();
        }
    }
}
{% endhighlight %}

__3. Now it's time__ to wrap the web service

{% highlight java %}
public final class AdaptedWebService {
    private final SomeWebService ws; 

    public AdaptedWebService(final SomeWebService ws) {
        this.ws = ws;
    }

    /*** the juicy part ***/

    public final Optional<Book> book(final Long bookId) {
        ...
    }

    public final Optional<Album> album(final Long albumId) {
        ...
    }

    public final Optional<Movie> movie(final Long movieId) {
        ...
    }
{% endhighlight %}

<br>
__3.1. With Anonymous Classes__ I would implement those methods like:

{% highlight java %}

public final Optional<Book> book(final Long bookId) {
    return Try.toGet(new FallibleAction<Book>() {
             @Override
             public Book result() throws Exception {
                 return ws.book(bookId);
             }
        });
}

public final Optional<Album> album(final Long albumId) {
    return Try.toGet(new FallibleAction<Album>() {
             @Override
             public Album result() throws Exception {
                 return ws.album(albumId);
             }
        });
}

public final Optional<Movie> movie(final Long movieId) {
    return Try.toGet(new FallibleAction<Movie>() {
             @Override
             public Movie result() throws Exception {
                 return ws.album(movieId);
             }
        });
}
{% endhighlight %}



This is already much better than to have to create specific classes implementing FallibleAction for each of this actions (and there are only three here, now imagine how 10 would look).

### And finally...!

Still, this is a lot of code and a lot of repetition.

__So this is how this methods look by using lambda expressions__ instead of Anonymous classes to pass functionality as an argument:

{% highlight java %}
public final Optional<Book> book(final Long bookId) {
    return Try.toGet(() -> ws.book(bookId));
}

public final Optional<Album> album(final Long albumId) {
    return Try.toGet(() -> ws.album(albumId));
}

public final Optional<Movie> movie(final Long movieId) {
    return Try.toGet(() -> ws.movie(movieId));
}
{% endhighlight %} 


Doesn't this look beautiful? 
We (not) just reduced the amount of code by about 13 lines and made it highly expressive, concise and objective.

__Note:__
 A Java lambda in the form () -> ... means that it has no arguments.
An example with argument is applied like this, where it receives an Integer and returns that plus one:


{% highlight java %}
list.stream().map(x -> x+1).collect(Collectors.toList());
{% endhighlight %} 

## Conclusion
 
So lambda expressions in Java actually are an anonymous object with a single method, being represented by what that method does. 

Love the lambda!