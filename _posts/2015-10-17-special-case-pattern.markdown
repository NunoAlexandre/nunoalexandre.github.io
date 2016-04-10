---
layout: post
title:  "Special Case Pattern"
date:   2015-10-17 20:00:39 +0100
categories: design-patterns
---
>Can we make the code that simple? It turns out that we can. We can change the ExpenseReportDAO so that it always returns a MealExpense object. If there are no meal expenses, it returns a MealExpense object that returns the per diem as its total:

{% highlight java %}
public class PerDiemMealExpenses implements MealExpenses {
    public int getTotal() {
    	// return the per diem default
    }
}
{% endhighlight %}

>This is called the SPECIAL CASE PATTERN [Fowler]. You create a class or configure an object so that it handles a special case for you. When you do, the client code doesn’t have to deal with exceptional behavior. That behavior is encapsulated in the special case object.

**from The Clean Code, Uncle Bob Martin.**

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

