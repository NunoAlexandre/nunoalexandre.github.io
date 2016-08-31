---
layout: post
title:  "Why Optional is better than Null"
date:   2016-08-31 22:03:39 +0100
categories: languages object-thinking monad functional-programming
comments: true
---

``` null ``` returning and checking is everywhere and it developed deep roots in the imperative programming world.
It's more than time to stop using it and to look into better ways of designing fallible computation result types.

Some attempts were made to work around this such as the ```Null Object Pattern ``` which I don't really like because it's a solution to be applied to every class that might be used as return type where null might be returned instead.


## What's the problem with using Null?

I see a few to start with:

* Null is not an object
* It makes code implicit and therefore not trustable
* It requires defensive programming (null checking), making it dirty

Let's consider the following method declaration:

{% highlight java %}
public final Category findCategoryById(long categoryId);
{% endhighlight %} 

This method declares that it returns a ```Category``` object, having it's category id.<br>
But, is it really? I mean, can I trust this code and relax? 

What if I implement that method like this?
{% highlight java %}
public final Category findCategoryById(long categoryId) {
    return null;
}
{% endhighlight %}
In such case I'm not - at all - returning a ```Category```, so the code is not saying the truth. <br>And that is bad.

We need to consider that it might happen that no such category exists with the given id.

One way to solve this would be to have, instead:

{% highlight java %}
public final Category findCategoryById(long categoryId) throws NoSuchElementException; 
{% endhighlight %}

This would make it more explicit but brings a few drawbacks: 

* Exceptions are known to be expensive
* Either requires try-catch blocks (big and ugly) OR
* You (can) keep postponing the exception handling (by adding throws NoSuchElementException to the call chain)


## Optional to the rescue

Optional is here to help us and we shouldn't just keep it aside.
Optional<T> is a wrapper around a value of type T that might actually be nothing. This not only encapsulates the null checking in a single place but also makes us design and reason rigorously about our code. 

For example, in Haskell there is no such thing as a ```null``` but the ``` Maybe a ``` type:
{% highlight haskell %}
data Maybe a = Just a | Nothing
{% endhighlight %}


I don't think of Optional as any modern solution to replace the usage of null verifications but as a design solution that should have been there from the beginning.

### Before and After Optional

Our method declaration should now become:<br>
{% highlight haskell %}
public final Optional<Category> findCategoryById(long categoryId);
{% endhighlight %}

So that, instead of doing the usual:

{% highlight java %}
Category> category = findCategoryById(x);
if ( category == null ) {
    log(<< Wrong category id >>);
}
else {
    log(category.name());
}
{% endhighlight %}

Or

{% highlight java %}
try {
    Category> category = findCategoryById(x);
    log(category.name());
}
catch ( Exception _ ) {
    log("<< Wrong category id >>");
}
{% endhighlight %}


We can better have:

{% highlight java %}
Optional<Category> category = findCategoryById(x);
log.info(category.map(Category::name).orElse("<< Wrong category id >>"));
{% endhighlight %}

Now imagine the impact that such different design and approach will have in your system as a whole...  

## What if we can't change the method declaration?

Let's suppose you are using some web service or some database API that doesn't yet use Optional and / or throws exceptions and you don't want either to check nulls all around the code either deal with the exceptions.

The solution is to wrap it up! 
Create ``` wrapper ``` classes that will bridge the outside data source nulls or exceptions into your internal convention ( [null|Exception] -> Optional).

Suppose that the method above is such case. <br>
I could wrap that (service) method like this:

{% highlight java %}
class MyService {
    private final OutsideService service;
    (...)

    //Example #0 - if it returns null
    public final Optional<Category> findCategoryById(long categoryId) {
        return Optional.ofNullable(service.findCategoryById(categoryId));
    }

    //Example #1 - if it throws an exception
    public final Optional<Category> findCategoryById(long categoryId) {
        try {
            return Optional.ofNullable(service.findCategoryById(categoryId));
        } 
        catch ( Exception _ ) {
            return Optional.empty();
        }
    } 
}
{% endhighlight %}

## Some final considerations

If you find it too much effort to opt for this wrapping logic either because the wrapped services have too many methods or you just find it too much to code, I understand your point. 
I can't choose for you but we may agree that it looks better to ensure code correctness than to resign to the way it's done elsewhere and do the same internally. 

If you just don't like the idea of repeating all those ``` try-catch ``` blocks, I get you there, but it's something easy to abstract (and I will soon post something about it).


## Is Optional enough for every situation?

No. Optional represents results of computation that might or not have failed but when you are left alone with an Optional.empty() you don't really have much information about the context of the failure. 
In some cases, it is not even necessary to have such context but in other cases it is and there are ways to approach that.

In Haskell, for such cases there is the ```Either``` type, defined as follows:

{% highlight haskell %}
data Either a b = Left a | Right b deriving (Eq, Ord, Read, Show)
{% endhighlight %}

Although Java officially doesn't have yet such type, [some have been written already](https://github.com/twitter/commons/blob/master/src/java/com/twitter/common/base/Either.java), declared like this:

{% highlight java %}
public final class Either<L, R> {...}
{% endhighlight %}

The idea is that your method returns an Either<L,R> object where L represents the successful computation result value and R the failing one (ordered by convention).

Thanks for reading and keep code beautiful.
