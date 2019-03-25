---
layout: post
title:  "Announcing Stork"
date:   2019-03-25 19:46:00 +0100
categories: parsing json swift github open-source
comments: true
---

_Delivering types from JSON like a Stork_ is the tagline of my most recent pet project, [Stork](https://github.com/NunoAlexandre/stork).
Stork is a lightweight library written in Swift 4.2 focused on making the flight from JSON to Types as smooth as possible. 
Stork believes in simplicity, explicitness, and control.

Based on functional programming principles and mildly inspired in Aeson, Stork is the middle sweet between JSON parsers such as Argo - full-fledged but requiring extra dependencies, learning too many operators, and too functional for some - and other parsers that require your types to be mutable, to throw on init, or that take the control away from you by making too many assumptions.

#### Links

- <a href="https://github.com/NunoAlexandre/Stork" target="_blank">Stork on Github</a>
- [How it works](#how-it-works)
- [Considerations](#considerations)
- [Why](#why)

## How it works

To go from JSON to types, all you need to do is to state what fields you want to parse. Stork infers their type and parses them for you. To make that possible, your types must follow the `FromJson` protocol.

{% highlight swift %}
protocol FromJson {
  static func from(value: JsonValue) -> Self?
}
{% endhighlight %}

In practice, this means that for a type to be parseable from JSON, it needs to provide a way of being constructed from a [JsonValue](https://github.com/NunoAlexandre/stork/blob/master/Stork/Stork/Source/JsonValue.swift): `string`, `number`, `bool`, `JSON`/`[String: Any]`, or `[JsonValue]`.


### API

Suppose you have a type `User` already complying to `FromJson`.

You can get Stork to parse you a `User` or an array of `User`s as follows: 

{% highlight swift %}
// Single User
let maybeUser: User? = User.from(json: userJSON)

// Array of User's
let users: [User] = [User].from(jsonArray: usersJSON)
{% endhighlight %}

### Operators

Stork provides only 4 infix operators to extract and parse a value from a JSON object:
 
{% highlight swift %}
infix operator .?   // Maybe get a scalar or custom type
infix operator .!   // Same as .? but expects the value to be present
infix operator ..?  // Maybe get an array of scalar or custom types
infix operator ..!  // Same as ..? but expects the array to be present
{% endhighlight %}

All of these operators share the same arguments: `(json: JSON, key: String)`.

### In Action

Say that we want to parse `User`s from some JSON input, where `User` and its nested types are defined as follows:

<script src="https://gist.github.com/NunoAlexandre/43644efec05ddf2061299eeac55ca9e1.js"></script>

With Stork, to go from JSON to this model all we need to do is to have these types complying to the `FromJson` protocol.

<script src="https://gist.github.com/NunoAlexandre/05515eff0524a7ec98669df118880dbb.js"></script>


## Considerations

Note that failing to extract a required field or failing to parse it to the desired, inferred type, causes Stork to internally fail with an exception, which gets translated into an _Optional_ for you. That might be inconvenient as it gives you no clue as to what field caused Stork to fail to parse.

As I started using `Stork` in my own production projects at <a href="https://itunes.apple.com/us/app/kidiyo-spelen-leren/id1296241552?mt=8" target="_blank">Kidiyo</a>, my workaround this current behaviour is to write unit/integration tests that verify all the requirements and specifications for all edge cases. As long as the schema - and its variants - of the JSON at hand is well-defined, this should not set you back.


## Why

First and foremost, I needed to be able to parse nested types and having worked with proper and well-designed JSON parsers such as [Aeson](http://hackage.haskell.org/package/aeson), this sad approach was a no-go to me:

{% highlight swift %}
guard let title = jsonArray[0]["title"] as? String else { return } 
{% endhighlight %}


Therefore, I looked into Swift JSON parsers that seemed to get some attention from the iOS community, such as Argo, Gloss, Unbox, and a few others.
Argo was clearly - and admittedly - inspired in Aeson (a good thing), but required extra dependencies (a bad thing), so I left it for later as I wanted to see what the other two had to offer. So I tried to make my types parseable using Gloss and Unbox, and I wasn't happy with the experience. Far from me to be cynical about those projects, but the reality is that I felt impotent using those two libraries. More specifically, the issues I found were:

- a transversal assumption that you always only want to build a type from a JSON object and not from a string, number, boolean or array of values;
- a transversal assumption that RawRepresentable Enum's must be a direct map from my their JSON counterpart;
- no compile-time guarantees that a (nested) type is parseable, i.e., that it complies to the required protocol to make it so.

At this point, I thought it was fun to write my own parser and see what kind of problems I would face myself. Although Stork is somewhat similar to Argo in design, I intentionally decided not to check how Argo worked, neither externally nor internally, until I had reached [Stork's first working version](https://github.com/NunoAlexandre/stork/commit/d58f9bc980ad7905fd5ecbd63748835a7823a9c5). 

Inspired in Aeson, my idea with Stork was to speak to the community that it targets, where currying and a bazillion of functional operators is _not_ common language.

Finally, I named it `Stork` as that was my daughter's first toy and because I liked the metaphor of having a stork delivering types to those expecting it.

