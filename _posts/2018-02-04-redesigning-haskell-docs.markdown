---
layout: post
title:  Redesigning Haskell docs
date:   2018-02-04 19:50:52 +02
categories: haskell hackage haddock design ui pet project typography
comments: true
---

One of the first impressions most people mention having about Haskell is that it looks scary. Not just the concepts involved are overwhelming, but the documentation pages are not properly user-friendly and welcoming. Last December I decided to take action and propose a new design to the Hackage and the Haddock repositories.

**Edit:** find the reddit thread about this blog post [here](https://www.reddit.com/r/haskell/comments/7vatew/redesigning_haddock_docs_and_hackage_pages/).

More than a year ago I started playing with [`Elixir`](https://elixir-lang.org/) and I noticed quickly that all its official pages look consistent and welcoming. And after seven months working with Haskell daily at work, I realized that our community really misses something of that kind: a consistent, friendly and welcoming look.

Very recently some big names in the Haskell words, namely Simon Peyton Jones, have come forward with [a proposal called `SLURP`](https://github.com/simonmar/ecosystem-proposals/blob/slurp/proposals/0000-slurp.rst): a _Single Liberal Unified Registry of Haskell Packages_. In that proposal they say:

> Hackage has been extraordinarily successful as a single repository
through which to share Haskell packages. (...) But in recent years there has
been increasing friction over,
>
> - (..)
>-  Hackage's features, **especially the visual presentation** and package
   documentation.

What a coincidence and what a timing to take action! I found it significant that even to the big Haskell researchers the visual presentation of the current hackage is rather lacking behind.

### Pull requests

You can find my pull requests here:

- [hackage](https://github.com/haskell/hackage-server/pull/648)
- [haddock](https://github.com/haskell/haddock/pull/721)

### Preview

Using the links below you can preview how my latest changes look like on the given pages, as well how those pages currently look like.
I am hosting the preview pages myself, therefore those pages will keep on being updated accordingly to the changes I push. This is a better way to let others preview and actually try the changes compared to screenshots, which I was doing before.  

#### Hackage preview pages

- <a href="/pets/hackage/home.html" target="_blank">Hackage homepage</a> (<a href="https://hackage.haskell.org" target="_blank">before</a>)
- <a href="/pets/hackage/browse.html" target="_blank">Hackage browse page</a> (<a href="https://hackage.haskell.org/packages/browse" target="_blank">before</a>)
- <a href="/pets/hackage/monoid-extras.html" target="_blank">Monoid.Extras package</a> (<a href="https://hackage.haskell.org/package/monoid-extras" target="_blank">before</a>)

#### Haddock preview pages

- <a href="/pets/haddock/Control.Lens.Cons.html" target="_blank">Control.Lens.Cons</a> (<a href="https://hackage.haskell.org/package/lens-4.16/docs/Control-Lens-Cons.html" target="_blank">before</a>)
- <a href="/pets/haddock/Control.Lens.Operators.html" target="_blank">Control.Lens.Operators</a> (<a href="https://hackage.haskell.org/package/lens-4.16/docs/Control-Lens-Operators.html" target="_blank">before</a>)
- <a href="/pets/haddock/Control.Lens.Tutorial.html" target="_blank">Control.Lens.Tutorial</a> (<a href="https://hackage.haskell.org/package/lens-tutorial-1.0.3/docs/Control-Lens-Tutorial.html" target="_blank">before</a>)
- <a href="/pets/haddock/Numeric.Kalman.html" target="_blank">Numeric.Kalman</a> (<a href="https://hackage.haskell.org/package/kalman-1.0.0.2/docs/Numeric-Kalman.html" target="_blank">before</a>)

### Feedback

Thanks to [Arian](https://arianvp.me), a wave of people have come to find my pull requests and drop their feedback.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">My colleague Nuno has taken a stab at redesigning hackage and haddock UI and would love to have your feedback <a href="https://t.co/2UM6O7mBIT">https://t.co/2UM6O7mBIT</a> <a href="https://t.co/7WMX2VzAFM">pic.twitter.com/7WMX2VzAFM</a></p>&mdash; Arian van Putten (@ProgrammerDude) <a href="https://twitter.com/ProgrammerDude/status/956807886889472001?ref_src=twsrc%5Etfw">January 26, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

So far the majority seems very positive about the new look&feel, but there is also feedback preferring the current theme.
The number one argument is that the current theme is dense in information, making it more suitable for quick glances and content searching.

What's your opinion? Drop your comments here, by email or on the pull requests linked above.
