---
layout: post
title:  "This Is Not Object Oriented"
date:   2017-02-12 16:54:39 +0100
categories: oo principles
comments: true
---

Look deeply into the principles of Object-Oriented programming. Then look into the code we are developing tagged as being Object-Oriented. Is it _really_ Object-Oriented? 

I dare to say no.

The three pillars of OOP are known as being:

1. Encapsulation
2. Inheritance
3. Polymorphism

Next to this, we have a vast set of principles such as SOLID, DRY, KISS, etc.

Do these matter at all? They do. Do we act as if they matter? I don't think so.

I have mixed feeling about this discussion. In one hand, I think we don't realise that those 3 pillars of OO are not really exclusive to OO or that OOP made them possible. And inheritance is no good, it's like advertising your product saying it is plugged into a rock.

However, we all keep using OO languages for everything and we defend it on the basis of these key principles while breaking them every single day while calling it "being pragmatic" or just by being forced by "great" frameworks like Spring.

And that pisses me off a little. It's like using a hammer for every home task but then ending up using the hammer the way around.

Allow me to show you how we do this with OO and how it is not OO.

### Encapsulation 

We had encapsulation in C, in a much more well-defined way than we do in Java for example, where we have to define a header file (*.h) declaring the methods you make available. 

The idea with encapsulation is to hide from the outside world the data and implementation details of your object. 

We all know this, but still, this is what we do all the time:

{% highlight java %}
public class User {
    private String name;
    private String password;
    private boolean isActive;

    public User(String name, String password, boolean isActive) {
        this.name = name;
        this.password = password;
        this.isActive = isActive;
    }

    public String getName() {
        return getName;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    private boolean getActive() {
        return isActive;
    }

    private void setActive(boolean isActive) {
        this.isActive = isActive;
    }
}
{% endhighlight %}

Bye, bye Encapsulation. This is not an object, it's just a data structure. I, as an outsider, know everything about the internals of this _object_.

How is this different from using a C struct?

Plus, important to mention this ```get``` and ```set``` disease. 
Seriously, this is such a procedural and dusty naming. 

Search for the definition of ```Get``` and ```Set``` in your dictionary and be amazed about how many different meanings it can hold. And don't we see that it doesn't read as one would expect in a world of objects? 

I hope one to ask me "how old are you?", not "get age". Or that someone tells me a joke to make me laugh, not _set_ my lips into a smile shape.

### Inheritance

Although it's factual that Composition is to be preferred over Inheritance (that is even a principle of OO), we still prefer or are forced to choose Inheritance.

We say OO is great because it's natural. Well...! 
Let's map inheritance into the real world:
You think of birds as flying animals, so you map that into a super class Bird that implements a ```fly()``` method. It happens that not all birds actually fly. Now what?

And, in technical terms, Inheriting is to define a hard coded compile-time dependency. With composition, you can make use of run-time Polymorphism instead, which is much more flexible and powerful.

Sandi Metz brilliantly showed in this talk not only how composition is better but also how Inheritance is not the way to abstract and reuse behaviour.
<div><iframe src="https://www.youtube.com/embed/OMPfEXIlTVE?ecver=2" width="640" height="360" frameborder="0" allowfullscreen></iframe></div>

Still, Inheritance keeps being a cornerstone of several and important frameworks such as Spring, Android, etc.

### Polymorphism

Polymorphism is perhaps _the_ nicest principle of OO when compared to procedural languages like C.
We could archive polymorphism in C. The initial C++ compiler would actually translate the C++ code into C, which just shows that it is possible. But in OO it is more elegant and straightforward.

Polymorphism allows us to focus on what needs to be done, instead of in who is going to do it.
We can make use of polymorphism by different ways: Ad-hoc (aka function level), through sub-typing or through parametric types (generics).

So how are we misusing it? 

Well, I don't see the Ad-hoc approach being used. Instead, we prefer to have one big method that checks the _instanceof_ the object.

Sub-typing, in the mainstream frameworks, is done through Inheritance. 

The parametric way is - I think - the one less commonly misused but maybe not used enough.

I (dis)like particularly to see this sort of thing bellow masked as polymorphism, in Java Spring:

{% highlight java %}
@RestController
public class ExampleController {
    @Autowired
    @Qualifier("aSpecificExampleService")
    private ExampleService service;
}
{% endhighlight %}

Being ```ExampleService``` an interface.

This creates the illusion of flexibility but it is not different from doing:

{% highlight java %}
@RestController
public class ExampleController {
    private ExampleService service = new SpecificExampleService();
}
{% endhighlight %}

It is actually different. You can change which service is identified as _aSpecificExampleService_ without having to change the classes which use it. 

However, when following the second example we have compile-time checking while that with the first approach only at loading time or run-time you will get to know if the targeted implementation is not present or if something is wrong.

We still benefit from having service as ExampleService, but to sell this @Autowired and @Qualifier strategy as something great is just misguiding and I think it's important that we as developers understand why.
 

### And More

These examples were just focused in the cornerstones of OO, but there is much more.

One common example is to use Util classes. It doesn't matter that _great_ libraries or frameworks are using it all the time! That doesn't make it correct.

Util classes are a collection of static methods that act upon the arguments they receive.
Since their arguments in OO are objects, it means that they have to know about the Object's data in order to know what to do.

This means to break a few of principles at once such as _Encapsulation_ and _Tell, Don't Ask_. You spread the knowledge about your objects across classes and since static methods are fixed to a class, you also lose flexibility and subtyping polymorphism.

No less important, static methods are procedures. Just like C methods are. 
To be clear: __This is not Object-Oriented.__

It is, instead, some sort of ugly mix between Procedural and OO programming.  
OO is not about exposing the objects' internals, neither util classes, neither methods ten lines long.

Object-Oriented is mostly about message passing. It's about getting objects encapsulation data and/or behaviour and getting them interacting with each other, building complexity along the way.

Although I think that us, as developers, have the biggest slice of blame for turning OO into this ugly thing, I also think that OO itself takes some of the blame.

Object-Oriented is not very well defined and not very practical. There are many different definitions of what OOP is, and each one adds a new view and often contradictory to each other.

And it's not super practical because inevitably we tend to seek shortcuts through it, like static methods.
To go purely OO, we have to look into languages like Smalltalk.

In opposite, have a look at Functional Programming and notice how well defined it is when compared to OO.

In conclusion, I think it's not practical to be purely OO, but that doesn't mean it's fine to be only 10%.

We have to do better, we have to read [David West's Object Thinking](http://www.goodreads.com/book/show/43940.Object_Thinking), to read [Sandi Metz](https://www.sandimetz.com) and even to get slightly infected by the extremism of [Yegor](http://www.yegor256.com).  

We have to feel itchy the next time we are going to create a Util class or to generate getters and setters, and we have to stop to realise that this is that we are doing is not object-oriented.