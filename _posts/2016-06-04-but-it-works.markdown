---
layout: post
title:  "But it works!"
date:   2016-06-06 22:15:39 +0100
categories: clean-code human-factors refactoring
comments: true
---


"But it works!" is a small big excuse to avoid excellence and to show our attitude towards code: either we just don't think it deserves all that much care or we think of if as a mystery box - so lucky we are that it is working!


If we get to the point of saying this and not really think that it is necessary to do any refactoring, either there is a very good reason or we are just not professional and we have let the code go wrong too long.

We must be able to look at our code and be sure of what it does.<br> 
Our functions must be concise, short, objective.

### Example 1 - Some Math
 What are the differences between these two functions?

{% highlight math %}
f(x) = x + (x-sqrt(x^2)) + (x*2)/x
{% endhighlight %}

{% highlight math %}
f(x) = x + 2
{% endhighlight %}


In terms of their result, they do exactly the same (__#A__) : they sum 2 to a given number. But they differ in what they _don't do_.
One is obvious, the other is not. One does exactly what it must, the other does more than the required to accomplish the goal, where more is less.

Why wouldn't you write it as simple as it can be?

(__#A__) Did you fall into the trap? It is pretty easy to, and that is why it is important to avoid this.
The two functions output the same for positive numbers but they act differently for numbers equal to 0 or less. 


### Clean code is simple

Complicated solutions complicate everything. They are hard to read, hard to grasp. Therefore, they are harder to test and to prove correct. 

The requirements of the software that we develop change often and sometimes change considerably. 
This means that our code must change along, and we can either just change what we think that - apparently(!) - is enough to satisfy the new requirements or we go a bit deeper and rethink the code in terms of the new problem to solve. 

  I found myself many times ending up with really messy solutions, with a bunch of complete unnecessary code, specially when solving some hairy bugs. It is not easy to take a step back and rethink the code but it is absolutely worthy to do so. 

If we are afraid to take the second way, that is pretty revealing and shouldn’t be kept. This fear of breaking something is valid, mainly when developing a large-scale system or when the language used or the design is side effects based. That is why automated testing is so important: 
they take away the need of being afraid of changing the code and leave you alone with your code culture. 

If we systematically take the first way we are feeding ignorance and create a place to code to rot, because the end solution will be the result of parts of many previous solutions.  


Another way of ending up with code like this can happen simply when one writes more code than necessary, ending up being super obvious, redundant or not very objective.


### Example 2 - isThree ?

Consider this function that checks if a given number is the number 3:

In Haskell:

{% highlight haskell %}
isThree :: Int -> Bool
isThree 3 = True
isThree _ = False
{% endhighlight %}


or even:

{% highlight haskell %}
isThree :: Int -> Bool
isThree x = if x == 3 then True else False
{% endhighlight %}


Or in Java:

{% highlight java %}
boolean isThree (int number) {
  if ( number == 3 ) {
    return true;
  }
  else {
    return false;
  }
}
{% endhighlight %}


<br>
What is wrong? It happens to be that this code is neither concise, short or objective. At least not as it can go.
"But it works!". Yes, it does, but why shouldn't we be open to improve our code as good engineers/developers/programmers?

For the Java version, this refactored version is not enough:

{% highlight java %}
boolean isThree (int number) {
  return number == 3 ? true : false;
}
{% endhighlight %}


First, there is still a place for error since you still can misplace the ```true : false``` and second, this is unnecessary.
If what we want is to evaluate a logic condition and output its result, we shouldn't be calculating the condition result and based on that return a value that is directly related. If we wanted to do something based on that condition, that a different story. 

Consider the steps taken when calling ```isThree(3)```:
0. return 3 == 3 ? true : false;
1. return true ? true : false;
2. return true;

This translate "if x equals to 3 return true, otherwise return false". 
What about simply “return x equals to 3" ?  __Avoid unnecessary computation.__


So in Haskell we would have:


{% highlight haskell %}
isThree :: Int -> Bool
isThree = (==3)
{% endhighlight %}

And in Java:

{% highlight haskell %}
boolean isThree (int x) {
  return x == 3;
}
{% endhighlight %}


__Avoid unnecessary computation.__ It will make everyone happier and the code cleaner.



### Example 3 - isEven 

Let's now check if a given number is even:

in Java:
{% highlight java %}
public boolean isEven( int x ) {
	x = Math.abs(x);
	boolean isEven = false;
	for ( int i = 2; i <= x; i+=2 ) {
		isEven = i == x;
	}
	return isEven;
}
{% endhighlight %}

in Haskell:
{% highlight haskell %}
isEven :: Int -> Bool
isEven x = elem absX [2,4..absX]
	where absX = abs x
{% endhighlight %}



… “But it works”! Yes, it does, but why would we want this to be our code when it can be this simple instead? 

{% highlight java %}
public boolean isEven( int x ) {
	return x % 2 == 0;
}
{% endhighlight %}

{% highlight haskell %}
isEven :: Int -> Bool
isEven x = mod x 2 == 0 
{% endhighlight %} 




### Example 4 - Not making use of the language

It is also important to be aware of what the language we are using can do for us, otherwise we end up doing things like this:

{% highlight java %}
public List<Integer> getList() {
	List<Integer> newList = new ArrayList<>();
	for (int i = 0; i < this.list.size(); i++) {
		newList.add(this.list.get(i));
	}
	return newList;
}
{% endhighlight %}

instead of this:

{% highlight java %}
public List<Integer> getList() {
	return new ArrayList<>(getList);
}
{% endhighlight %}

The first version does the work, but that is not how we want it. 

__Imagine that this situation gets repeated all around your code: you will end up taking 10 times more to read it, with 5 times more lines of code and with (much more) uncertainty.__ 

If the language doesn't support something specific that you need to do all around, encapsulate it and then you can use it in a clean way.

Little bad things together become terrible so avoid the "But it works" excuse and refactor your code instead, making it simpler and cleaner.<br>Don't do it only for yourself but also for your teammates and for the sake of the sotware you are developing together as a team.

