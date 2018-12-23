---
layout: post
title:  "Heart-Shaped Box"
date:   2018-12-23 10:45:39 +0100
categories: software modelling types haskell swift
comments: true
---

At University, a professor enthusiastically told us that translating problems into programming models would be one of the coolest tasks we would have as developers. Unfortunately, modelling is most often done poorly. Types to the rescue!

### Modeling

<img src="/images/treeToHaskell.svg"/>

Modelling is building the backbone of a world using a programming language. We give a body to a problem. We define a vocabulary and a shape to reason about that problem.
Modelling is, therefore, halfway into solving a problem. After all, we need a ball to score a goal.

> In a sense, programming (pure functional programming, in particular) is about transforming data from one form to another. Types allow us to describe the form of that data, and the more precise we make these descriptions, the more guidance the language can give in implementing transformations on that data.
<br/><br/><small>**Type-driven Development with Idris by Edwin Brady**</small>


What ways do we have to define those _descriptions_?

### Using Procedures

Modelling a problem using procedures really means not modelling at all.
However, this approach appears to be a practice amongst mobile developers.

A model helps to reason about the problem and to build the path to solve it.
Doing that procedurally means the goal is achieved solely via procedures. In such an approach, there is not a concrete nor explicit definition of the problem. The result is sufficient.

#### Modeling a slideshow

Say we want to build a slideshow. A `Slideshow` is a list of `Slide`'s, and each
slide contains a `title`, a `description`, and an `image`.

Because we want to display the slideshow in a GUI, each slide should have a UI representation, where the layout and the visual properties of its fields are defined.

The following is a procedural way to "model" this problem.


<script src="https://gist.github.com/NunoAlexandre/c36d7241e3b45d65d36a966aaa7ab893.js"></script>

To build a Slideshow, we'd then do:

<script src="https://gist.github.com/NunoAlexandre/b9d2213a3e177e5dcba44e01a6eae1fd.js"></script>

##### Problems

There a couple of problems with this approach:

- there is no overview on what a `Slide` is
- we are mixing shape and content
- it's tedious and error-prone to add, edit or reorder a slide, as each of its parts are spread
- it's impossible to create a slideshow dynamically
- no flexibility due to missing dependency-injection
- easy to have **x** titles, **y** descriptions, and **z** images and get away with it

### Using Types

By using types, we solve all the problems we have in the procedural approach without trading off any advantage:

<script src="https://gist.github.com/NunoAlexandre/a1fe8e3bb291cec028de81e4bd21c025.js"></script>

To create this `Slideshow`, we do:

<script src="https://gist.github.com/NunoAlexandre/482adc37420b713b32bb665f3565dffc.js"></script>


##### Advantages

- precise definition and overview of what a `Slide` _is_ and what each one _contains_
- easy to add, edit, and reorder
- a slideshow can now be built with data from any source, at compile or runtime
- clear separation between shape and content, and between the concept and its UI representation
- the compiler ensures each slide is correctly defined
