---
layout: post
title:  Painting with a broomstick
date:   2017-05-12 19:30:39 +02
categories: design oo pp
comments: true
---

I have worked on several Java projects, both small and quite large ones. In all of them, one thing was common: secretly but clearly, we are procedural programmers.

OOP is not as easy as it may seem. With the thousand of principles around, deciding where to place a piece of functionality easily becomes a philosophical discussion.
OOP is sold as a way of programming analogous to real life, but have we stopped to think if that is intricately good? Or if that is what we actually do?

### Naming

Naming is by far one of the biggest challenges in software development. That makes it a quality differentiator between a great and an average developer. But more importantly, now, it shows - without filters - how well a system is thought through.

Are you used to classes named like `*Handler`, `*Manager`, `*Util`, etc?
Now, how many times real-life objects are named like that? See, there is a naming problem. Our vocabulary is poor, and it reveals how procedural we are in an object's world.

In the real world, we call it `Door`, `Camera` and `Lamp`, not `PrivacyManager`, `PictureCapturer` and `LightProvider`.

Moreover, this is not just about names, but names reveal more: We are not thinking in terms of objects. `PrivacyManager` is not an object, it's a bag of procedures.

### Objects as Structs

In C, a low-level procedural programming language, in very general terms we write code by:

* defining data structures:

{% highlight c %}
struct product_t {
    char name[50];
    double price;
}
{% endhighlight %}

* creating procedures that receive data structures and apply changes on them or elsewhere in the world:

{% highlight c %}
void change_price(struct product_t * product, double new_price) {
        product->price = new_price;
}
{% endhighlight %}

C, such an old language. And procedural programming, how long ago! Right?

It happens that in Java often we do exactly the same:

{% highlight java %}
class Product {
    private String name;
    private double price;

    void setName(String name) {
        this.name = name;
    }

    String getName() {
        return name;
    }

    void setPrice(double newPrice) {
        this.price = newPrice;
    }

    double getPrice() {
        return price;
    }
}
{% endhighlight %}

followed by:

{% highlight java %}
class ProductService {
    //...

    void changePrice(Product product, double newPrice) {
        product.setPrice(newPrice);
    }
}
{% endhighlight %}


We use objects as data structures and create services, handlers, managers, etc, to hold procedures that do things that should be of the objects concerns.

This type of architecture is sadly very present - at least - in the Java' side of the industry, and is known as [Anemic Domain Model](https://martinfowler.com/bliki/AnemicDomainModel.html).

### And then what?

Good question. I hope that the two points before made sense to you. But you might wonder: then what? What is the problem with going procedural?

I see a few. First, although I was basically a baby when OO raised and got adopted, I believe there were many reasons for it getting preferred over PP. Going back to procedural might mean we are forgetting lessons taken by those before us.

Secondly, although PP serves its purpose where it is sensible to be used, we should know that it's not best suited for banking, web applications and so on.

Thirdly, there is a matter of misusage. Suppose I use a broom to paint a house. Brooms are great to clean the floor but will make my life harder as a painter. We recognize that it might not be the best idea and move to use a paint roller... but we decide to fold a pair of socks to the handle and paint using that part, holding on the actual rolling block. It's counterproductive, right? Still, that's the sort of thing we are doing as an industry. We are carrying the weight of an OO language like Java to do simple procedural code.

The solution is to either pick a safe procedural language or to make the effort to learn object thinking, which is a hard task as the main tools like Spring enforce us to think procedurally.  

Functional programming, although surely not suited for everything, seems to offer the best solution for the targeted area. Data is immutable and runs free, and complex systems are built composing concise and small units. No more unnecessary philosophical battles, no more gorillas holding bananas and no longer messy mixed worlds.   
