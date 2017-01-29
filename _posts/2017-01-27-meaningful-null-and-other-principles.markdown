---
layout: post
title: "Meaningful Null and Other Principles"
date: 2017-01-27 22:20:39 +0100
categories:  OO design programming java principles
comments: true
---

The discussion around using Null is a long one. 
I wrote before about [“Why Optional is better than Null”]({{site.url}}/2016/08/31/why-optional-is-better-than-null), but there is more to discuss regarding using Null as a way of expressing meaning.

If you are doing OO programming, you should not be using Null as you drink coffee in the morning.

In that other post on this subject, I focussed more on the common case where Null is used to express failing computation.
Another way of using Null is by making it a way of expressing meaning.
If you are doing that, stop it. There are proper, better ways of doing it.
  
### Request Example

Let’s take a look at this example: You want your users to send a request to another user, who either approves or rejects it.
I have seen such cases being coded like this:  

{% highlight java %}
public class Request {
    private String content;
    private Boolean hasBeenApproved;
    
    public Request(String content) {
        this.content = content;
        hasBeenApproved = null;
    }     

    public String content() {
        return content;
    }

    public void setDecision(boolean decision) {
        this.hasBeenApproved = decision; 
    }

    public Boolean hasBeenApproved() {
        return hasBeenApproved;
    }

    public Boolean alreadyAddressed() {
        return hasBeenApproved != null;
    }
}
{% endhighlight %}


What is wrong here? The problem really is that the Boolean attribute hasBeenApproved, by being null, holds meaning and business logic.

Furthermore, note that the setDecision method gets a primitive boolean since the responder can either approve or decline, not ignore it. Again, such logic is mapped in a hidden, implicit and weak manner.

Now, imagine that the request could be ignored... It would require a new boolean attribute (wasIgnored) to be added, which would only check that specific case... ugly!

By last but not least, [this object is not shy](https://media.pragprog.com/articles/may_04_oo1.pdf). Even as an outsider you easily guess how it works inside, which is not good as that causes unwanted coupling, not only around types but also around an implementation.

This code would be used as follows:

{% highlight java%}
Request aRequest = new Request("Offer me the SICP book");
aRequest.setDecision(true);
{% endhighlight %}

It doesn't read nicely. It sounds very procedural and it's not straightforward what ```true``` means there.
By checking the method definition, you know straight away how it is implemented inside the class.

Lets continue... 

### Handling new requirements
Now your client has new requirements: The Request responder must now be able to not only decline or approve it, but also mark it as _Need of Work_.

I guess this would change the code by creating first a RequestResponseType:

{% highlight java%}
public enum RequestResponseType {
    APPROVED, DECLINED, NEEDS_WORK;
}
{% endhighlight%}

And then adjusting the Request to:

{% highlight java %}
public class Request {
    private String content;
    private RequestResponseType response;
    
    public Request(String content) {
        this.content = content;
        hasBeenApproved = null;
    }     

    public String content() {
        return content;
    }

    public void setResponse(RequestResponseType response) {
        this.response = response; 
    }

    public Boolean hasBeenApproved() {
        return hasBeenApproved;
    }

    public RequestResponseType getResponse() {
        return response;
    }

    public boolean hasBeenAddressed() {
        return hasBeenApproved != null;
    }

}
{% endhighlight %}


This is getting dirty. Every change will cause the Request object to expose more of its internals, making the logic around it become spread instead of contained.

It could be used like this:

{% highlight java %}
Request aRequest = new Request("Offer me the SICP book");
aRequest.setResponse(RequestResponseType.APPROVED);
System.out.println("Was my request approved? " + (aRequest.getResponse() == RequestResponseType.APPROVED));
{% endhighlight %}


Ugly...! Note how this approach breaks a few principles: 
* encapsulation: _Request_ exposes its internals
* coupling: An outsider knows about RequestResponseType
* _Tell, Don't Ask: rather than asking an object for data and acting on that data, we should instead tell an object what to do_ ([Martin Fowler](https://martinfowler.com/bliki/TellDontAsk.html))

### Doing it better

It is true that following the way that is shown above does work. ["But it works"]({{site.url}}/2016/06/06/but-it-works) is not good enough. And I wouldn't be happy to maintain such code after three or four new requirements have arrived and turned it into cooked spaghetti.

In order to avoid breaking those principles listed above, I would refactor the previous approach into something like this:

{% highlight java %}
public class Request {
    private final String content;
    private RequestStatus status;

    public Request(final String content) {
        this.content = content;
        status = RequestStatus.AWAITING_RESPONSE;
    }

    public final String content() {
        return content;
    }

    public void approve() {
        status = RequestStatus.APPROVED;
    }

    public void decline() {
        status = RequestStatus.DECLINED;
    }

    public void needsWork() {
        status = RequestStatus.NEEDS_WORK;
    }

    public Boolean hasBeenApproved() {
        return status == RequestStatus.APPROVED;
    }

    public Boolean hasBeenDeclined() {
        return status == RequestStatus.DECLINED;
    }

    public Boolean doesNeedWork() {
        return status == RequestStatus.NEEDS_WORK;
    }

    public Boolean hasBeenAddressed() {
        return status != RequestStatus.AWAITING_RESPONSE;
    }

    private enum RequestStatus {
        AWAITING_RESPONSE, APPROVED, DECLINED, NEEDS_WORK;
    }
}
{% endhighlight %}

How does this help?

* The logic around "Awaiting Response" and around setting the response is no longer ugly and implicit. 
* With this approach, Request no longer exposes its internals. One doesn't know how the different response/statuses are mapped and implemented internally.
* Decreased coupling as there is no external knowledge of ResponseRequestType.
* Now one Tells _Request_, instead of asking for data. 


Note how different the code is used, now that the knowledge and decision logic lays where it belongs.

{% highlight java %}
Request aRequest = new Request("Offer me the SICP book");
aRequest.approve();
System.out.println("Was my request approved? " + (aRequest.hasBeenAccepted());
{% endhighlight %}



### Conclusion

This improved version is not great yet. 
The more statuses are introduced, the bigger _Request_ will become. And if _Request_ needs to include more data such as date, author, etc, this class no longer will respect the Single-Responsibility Principle. 
Furthermore, one can change the status of the Request, which may not be desired.

I might write a post later on how to improve it, so keep posted.




