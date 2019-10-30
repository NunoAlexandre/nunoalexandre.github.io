---
layout: post
title:  "The New Kidiyo Menu"
date:   2019-10-30 11:02:00 +0100
categories: design pitch concept ux ui dsl portfolio
comments: true
---

The original design of the Kidiyo app, released in July 2018, received several negative reviews on the stores and from direct user contact. Get to know what was wrong and how I and [Raissa Colonetti](https://raissacolonetti.com) got the new Kidiyo app released.

It was unanimous at Kidiyo that our app was not good enough, and the users could tell us what was wrong. Long story short, the app required the users to take a handful of decisions before reaching the content. That's too difficult for kids and the costs of a mistake (making the wrong choice at one of the steps) was too expensive. At a technical level, it was also not scalable, as each decision was implemented in a separate screen. That meant that adding new content required new decisions, i.e., new screens, which required a new version of the app to be released, which required long un unpredictable waiting review times from Apple.

I knew what I thought best for Kidiyo and its users. A menu and design a la Netflix/youtube, totally configurable from the backend, playful, easy, discoverable. And so I came out with the initial concept.

### Concept #0

This first concept made the selected player a central point of the screen at the start. The feed of items offered a row of filters based on the type of content: all, videos, music, ebooks, games, etc.

<img class="medium centered" src="/images/kidiyo-menu-0.jpeg"/>

It was a good start to trigger the team and get us going. The next steps were to pitch it, create a usable prototype, learn and improve.

### The Pitch

<iframe 
  src="//www.slideshare.net/slideshow/embed_code/key/c4Yncm3Gtey24L" 
  width="100%" 
  style="height: 55vh" 
  frameborder="0" 
  marginwidth="0" 
  marginheight="0" 
  scrolling="no" 
  allowfullscreen
> 
</iframe>

This presentation was followed by this demo video where the core concept is shown and explained.

<iframe 
  width="100%" 
  style="height: 55vh" 
  src="https://www.youtube.com/embed/JQ2rhVh4KUc" 
  frameborder="0" 
  allow="accelerometer; 
  autoplay; 
  encrypted-media; 
  gyroscope; 
  picture-in-picture" 
  allowfullscreen
 >
 </iframe>

The pitch was a success. However, the pedagogy advisory board had some critical comments on this new design. Mainly, they missed the idea of a character-based "world", which is studied to be rather important for kids' content.

### User testing

After some adjustments, we went ahead and organized a user testing day in our office. Some parents signed in and we had both the parents and the kids going through the prototype. We made questions, took notes, observed, learned.

The overall feedback was excellent. Parents and kids loved the new concept and about all requirements were achieved.

We had built a prototype, now we measured so that we could learn. We now had green lights to implement and deliver value to our users!

### Kidiyo v2.0.0 🎉

At first, we always referred to this project as _The New Kidiyo Menu_ when it fact it was more than just that.

I refactored the majority of both the iOS and Android apps to make this new version possible. All the thumbnails were redesigned and made consistent by [Raissa](https://raissacolonetti.com). To make the menu configurable from the backend, I designed a simple DSL (Domain Specific Language) that is parsed and interpreted by the apps to generate the desired menu.

In the end, we found the result to be beautiful and exciting as shown below.

<iframe 
  width="100%" 
  style="height: 55vh" 
  src="https://www.youtube.com/embed/VKU1IHyBtKk" 
  frameborder="0" 
  allow="accelerometer; 
  autoplay; 
  encrypted-media; 
  gyroscope; 
  picture-in-picture" 
  allowfullscreen
 >
 </iframe>

<div class="screenshots">
  <img class="column" src="/images/kidiyo-screenshots/1.png"/>
  <img class="column" src="/images/kidiyo-screenshots/2.png"/>
  <img class="column" src="/images/kidiyo-screenshots/3.png"/>
  <img class="column" src="/images/kidiyo-screenshots/4.png"/>
  <img class="column" src="/images/kidiyo-screenshots/5.png"/>
  <img class="column" src="/images/kidiyo-screenshots/6.png"/>
  <img class="column" src="/images/kidiyo-screenshots/7.png"/>
  <img class="column" src="/images/kidiyo-screenshots/8.png"/>
</div>

#### Credits

<strong>Concept developed by</strong>: [Nuno Alexandre](https://nunoalexandre.com) <br>
<strong>Implemented and designed by</strong>: [Nuno Alexandre](https://nunoalexandre.com) & [Raissa Colonetti](https://raissacolonetti.com)<br>
<strong>Company</strong>: [Kidiyo](https://kidiyo.com), All Rights Reserved.<br>