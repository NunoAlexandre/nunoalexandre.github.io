---
layout: post
title: "Encapsulating Try-Catch Statements"
date: 2016-12-20 23:20:39 +0100
categories: abstraction encapsulation programming java
comments: true
---

One of the things I enjoy the most in programming is to identify patterns, understand in a high-level what is being done or what is intended, and to abstract that into a contained solution.

I have created this repository, [NunoAlexandre/abstractions](https://github.com/NunoAlexandre/abstractions), where I will be committing some examples of such abstractions, both in a way of exercising, to have fun and potentially to help someone.

Some languages, like Lisp, are highly malleable but Java is not. There are no macros and no way to change or extend the compiler in order to improve the core of the language.

But there is still _some_ hope and some ways to improve it. 

### Try-Catch

Having said all above, what is a Try-Catch all about? 
First, it is a very procedural-oriented thingy.
It defines two blocks, the ```try``` block and the catch```.
And within the try block we place statements, from which one or more might fail, and in the catch, we handle those potential failures.

In terms of Java code, this translates to:

{% highlight java %}
try {
     //statements that potentially throw exceptions
}
catch(Exception e)‏ {
     //handling the exception occurrence
}
{% endhighlight %}


Easy, we all know it, no big deal.


### What's wrong with it?

Firstly, it is a procedural-oriented solution. Object-Oriented programming is all about objects communicating through messages, so this clearly is not OO.

Secondly, it takes space too much space. 
If you only have to handle potential exceptions once in your system, that's probably fine, but if it is something you have to be doing often, it gets big and dirty.

Thirdly, it is usually in a way that makes code ugly, not encapsulated and therefore harder to maintain and understand.


This is what is most frequently see:

{% highlight java %}
final Object anObject;
try {
   anObject = aMethodThatMightThrowAnException();
} catch (Exception e) {
   log.error(e);
}
{% endhighlight %}



And this is the moment to understand what is going on.

So we start by creating a variable without initializing.
Then we try to assign some value to it, which might cause an exception.
If it actually does cause an exception, we handle it.

### Abstracting

Having that understanding, we can abstract it and encapsulate it. Although that in the end of the line, the code is the same, the fact that it is encapsulated and contained is already a better solution.
A criminal outside the jail or inside is still a criminal, but does less harm to the society. That's how I see this.

It is important to also identify other possible cases:

* where we want to get a value from somewhere
* where we want to execute a statement, or another one in in the case when an exception arises.
* where we want to execute a statement or another one in the case when an exception arises but feeding it with the exception.


{% highlight java %}
public class Try {

    public static <T, E> Optional<T> toGet(FallibleAction<T, E> action) {
        try {
            return ofNullable(action.result());
        } catch (Exception e) {
            return empty();
        }
    }

    public static void toRun(FallibleRunnable runnable, Runnable ifFail) {
        try {
            runnable.run();
        } catch (Exception e) {
            ifFail.run();
        }
    }

    public static void toRun(FallibleRunnable runnable, Consumer<Exception> ifFail) {
        try {
            runnable.run();
        } catch (Exception e) {
            ifFail.accept(e);
        }
    }
}
{% endhighlight %} 



Being that ```FallibleAction``` and ```FallibleRunnable``` are functional interfaces defined as:


{% highlight java %}
@FunctionalInterface
public interface FallibleAction<T, E> {
    T result() throws Exception;
}

//and...

@FunctionalInterface
public interface FallibleRunnable {
    void run() throws Exception;
}
{% endhighlight %} 


### Using it

Suppose we have defined the following method:

{% highlight java %}
public static double div(int x, int y) throws IllegalArgumentException {
   if (y == 0) throw new IllegalArgumentException();
   return x/y;
}
{% endhighlight %}


Some examples of usage of the Try abstraction would be:

{% highlight java %}
//case 1
Try.toGet(() -> div(15,5)).ifPresent(System.out::println);

//case 2
Try.toRun(() -> div(5,0), () -> LOG.warn("Division by 0 is not defined"));

//case 3
Try.toRun(() -> div(5,0), e -> e.printStackTrace());
{% endhighlight %} 


This is now just a sketch but hopefully, you got the idea and you see how helpful it can be.

By having such abstraction we now can focus on what we actually want to do and not on how it is done. This is a step into being more declarative instead and away from the imperative style.

I have used this code also on [this post](/2016/09/16/love-the-lambda), which might help to have a richer image of it.