---
layout: post
title:  "A small step"
date:   2019-07-17 10:46:00 +0100
categories: readability code bikeshedding
comments: true
---

Breaking those long lines of code into smaller, grouped ones, with a clear consistency on where different types of elements are placed, is a small and efficient step towards improved readability.

### Before

Consider the code example below. There are **two calls**, **five arguments**, and **three levels** of nesting. However, that's not what we get from the looks of it. We need to spend time visually parsing it and group the parts in different categories, not to mention that scrolling is required to even read the whole thing.

<script src="https://gist.github.com/NunoAlexandre/70e73375596decf14e3934c9c373ebe6.js"></script>

Note the form of this code, leaving the actual code aside. No clue, whatsoever.
<img src="/images/small-step-before.png"/>

### From now on

We want to clearly see, without knowing the code, what is:

- A function call
- A function's arguments
- Part of what

For that, we need to separate calls from arguments by placing the arguments in a new, indented line:

<script src="https://gist.github.com/NunoAlexandre/8fda4bf645434b81fa09f9fc24448f95.js"></script>

Where now the form of this code says it all, whatever the actual code is:

<img src="/images/small-step-after.png"/>