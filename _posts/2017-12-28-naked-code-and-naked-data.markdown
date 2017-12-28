---
layout: post
title:  Naked code and naked data
date:   2017-12-28 14:50:39 +02
categories: code principles declarative
comments: true
---

Naked code and naked data are a somewhat discrete code smell that spread quickly and that decrease the quality of a project. Let's see why and how we can avoid them through wrapped code.

### Naked data

Naked data is data that is represented and passed using primitive types. Their domain is implicit or even hidden.
This leads to three problems:

1. Poor type vocabulary
2. Low type safety
3. Bugs

Let's jump to an example. Suppose you are given the following function:

{% highlight haskell %}
runJob :: Int -> Int -> String -> String -> IO ()
runJob projectId companyId jobType priority = -- do something
{% endhighlight %}

Unless if you check the implementation of this function, you don't know in which order to pass the arguments.
Furthermore, `projectId` and `companyId` are actually just `Int`s, meaning they are seen as the same by readers and by the compiler.

This program presents a very low vocabulary and opens up a whole world of errors such as:

{% highlight haskell %}
let projectId = 1
let companyId = 2
let jobType = "download"
let priority = "normal"

-- wrong order
runJob companyId projectId priority jobType

let otherCompanyId = 1

-- compiles and evaluates to True
companyId == projectId
{% endhighlight %}

You might be thinking: "You can cover these cases through exhaustive testing". That might help a little, but it's way better to **make it right by design**.

This example above could be made right by design by wrapping data in its own domain:

{% highlight haskell %}
newtype ProjectId = ProjectId Int
newtype CompanyId = CompanyId Int
data JobType = Download | Upload | Save
data Priority = Low | Normal | High

runJob :: ProjectId -> CompanyId -> JobType -> Priority
{% endhighlight %}

This way we have both a rich vocabulary and type safety, which avoids all the mistakes that were possible before.
This sure adds a tiny overhead, but one thing I have been learning with my Team Lead [Robert life](https://nl.linkedin.com/in/robert-kreuzer-9a729027) is that life is made of tradeoffs. And here the _pros_ are much stronger than the _cons_.


### Naked code

The concept of _naked code_ is similar to the concept of _naked data_, but I'd say it's slightly more subjective.

Naked code is code written and implemented without a domain. It has the potential to be something on its own, but, unfortunately, it's not.

I have mainly three problems with naked code:

1. Harder to test
2. Hard to read
3. Impossible to reuse

An example of naked code would be:

{% highlight haskell %}
-- |  Active companies with failed jobs should be alerted.
getCompaniesToAlert :: [CompanyId] -> [Job] -> [CompanyId]
getCompaniesToAlert activeCompanies allJobs =
  intersect activeCompanies (nub . map jobCompanyId . filter ((== Failed) . jobStatus) $ allJobs)
{% endhighlight %}

Good luck reading this, and, even worst, reading a system where code like this is the norm.
For someone reading this, it's unclear what this function is really about. I think of this kind of code like that kind of people who keep making parenthesis while telling a story and neither them neither you can understand where it is going.

Instead, give every piece of knowledge and behaviour its own place and let composition be declarative and objective:

{% highlight haskell %}
getCompaniesToAlert :: [CompanyId] -> [Job] -> [CompanyId]
getCompaniesToAlert subscribedCompanies allJobs =
  intersect subscribedCompanies getCompaniesWithFailingJobs

getFailedJobs :: [Job] -> [Job]
getFailedJobs = filter ((== Failed) . jobStatus)

getCompaniesWithFailingJobs :: [Job] -> [CompanyId]
getCompaniesWithFailingJobs = nub . map jobCompanyId . getFailedJobs
{% endhighlight %}

Again, this adds a small overhead, but the _pros_ are more than the _cons_ since the code is now:

1. Testable
2. Declarative
3. Readable
4. Reusable


### Salt & Olive oil

This approach must be taken with a pinch of salt (and olive oil).
Measure well the trade-off you are making in your specific case and choose accordingly. Sometimes, breaking everything to its own function is not the best route and it's important to be balanced in this case.
