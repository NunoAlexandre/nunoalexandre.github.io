---
layout: post
title:  A case for Dependent Types
date:   2018-01-28 14:33:52 +02
categories: haskell dependent-types type-safety gadts datakinds java
comments: true
---

I used to think of Haskell ADTs (Algebraic Data Types) as a sort of a final, flat type inheritance definition. Sometimes they are more like a base-class design in OO without the good bits. Here I show how we can overcome that by using _dependent types_.

### A wrong analogy

Consider the following ADT:

{% highlight Haskell %}
data Value
  = StringValue String
  | IntValue Int
{% endhighlight %}

I used to think there were - _sort of_ - three types here.
Or, maybe better put, I used to think that this was a kind of a Haskell equivalent of this approach in Java:

{% highlight java %}
abstract class Value {}

final class StringValue extends Value {
  final String value;

  public StringValue(String value) {
    this.value = value;
  }
}

final class IntValue extends IntValue {
  final Int value;

  public IntValue(Int value) {
    this.value = value;
  }
}
{% endhighlight %}


In the Java example, we have three types we can play around with.
**But** on the Haskell ADT above, there is only one type: `Value`. `StringValue` and `IntValue` are just different constructors of that type.

#### Implications

In terms of code, this means we can have:

{% highlight haskell %}
f :: Value -> Value
{% endhighlight %}

But we can't have a specialized `g` that only accepts, say, `StringValue`s:

{% highlight haskell%}
g :: StringValue -> StringValue
{% endhighlight %}


This is fairly annoying, as now the only way of applying specialized functions to a specific `Value` ~~type~~ constructor is by unwrapping it beforehand, which still requires the parent operation to accept any `Value` if we want to be _total_. This is ugly and repetitive:

{% highlight haskell%}
g :: Value -> Value
-- the real reason for this function
g (StringValue str) = StringValue $ str ++ "?"
-- A kind of python `pass`
g (IntValue int) = IntValue int
{% endhighlight %}

This implies that once you define your ADT, you have to stick with it everywhere, even when parts of it are not applicable or sensible to an operation.

In the example above, the return type is also `Value`, so we can sort of `pass` it for the cases that we are not interested in. But what if we want to have a function `k :: Value -> String`, that is meant to only accept `StringValue`s?

A workaround would be to set such case as `undefined` or to throw `error` for the `IntValue` case, as dynamically typed languages do. Another option would be to return a `Maybe String`, where `Nothing` represents the non-applicable-case.

None of these options are clean or even acceptable solutions, in my opinion. They are neither type-safe neither made right by design.

### A better way

This problem can be very neatly addressed by bringing _dependent types_ onto the table, by using `GADTs` (Generalized algebraic datatypes) and `DataKinds`:

{% highlight haskell %}
{-# LANGUAGE GADTs             #-}
{-# LANGUAGE DataKinds         #-}
{-# LANGUAGE FlexibleInstances #-}
{-# LANGUAGE KindSignatures    #-}

data ValueType = StringType | IntType

data Value :: ValueType -> * where
  StringValue :: String -> Value 'StringType
  IntValue :: Int -> Value 'IntType
{% endhighlight %}


Going step by step:

1. First, we define the type of values (`ValueType`) we support: `StringType` and `IntType`.

2. Then, we define `Value`, whose type _depends on_ a concrete `ValueType` to be resolved.

3. Now, we define the value wrappers we need and specialize their type accordingly:
- `StringValue` is a `Value 'StringType'` which wraps a `String`.
- `IntValue` is a `Value 'IntType'` which wraps an `Int`.

**Notes**:
- If you are wondering about the `'` behind each `ValueType`, it is just a way of being explicit that we are promoting the `StringType` constructor, which is possible by using the `DataKinds` extension.
- The `FlexibleInstances` extension is used so that we could define the type signature of `Value` by its _kind_.

#### The whole and the self

By using GADTs and DataKinds, each type of value can now stand for itself, instead of just existing in the context of the whole.

That means that we can have functions that accept all types of `Value`s:

{% highlight haskell%}
resetWithDefault :: Value a -> b
resetWithDefault (StringValue _) = StringValue "Hello, world!"
resetWithDefault (IntValue _) = IntValue 3
{% endhighlight %}

As well as ones that only accept specific types of values:

{% highlight haskell%}
serious :: Value 'StringType -> Value 'StringType
serious (StringValue str) = SStringValue $ str ++ "!"

plusX :: Value 'IntType -> Int -> Value 'IntType
plusX (IntValue x) y = IntValue $ x + y
{% endhighlight %}

#### Batteries included

Note that this approach brings further type-safety to our system.
While before one could have the following and get away with it silently:

{% highlight haskell %}
foo :: Value -> Value
foo (StringValue _) = IntValue 0
{% endhighlight %}

Now, to achieve the same result, one has to be strict and explicit:

{% highlight haskell %}
foo :: Value 'StringType -> Value 'IntType
foo (StringValue _) = IntValue 3
{% endhighlight %}

We can also guarantee that, for functions that accept all kinds of `Value`s, one can't change the type of the input value when calculating the result value:

{% highlight haskell %}
bar :: Value a -> Value a
-- Compiler error, as `IntValue` does not match `StringValue`
bar (StringValue str) = IntValue 3
{% endhighlight %}

### Final notes

I am not against the use of `data`. I love it, and I wish that such terse and powerful way of defining types existed in more mainstream languages. But as you see, there are cases where they are not ideal left alone and where a higher level of type-safety is both necessary and advisable.
