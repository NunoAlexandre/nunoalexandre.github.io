---
layout: post
title:  "Forcing an orientation in iOS"
date:   2018-07-14 21:36:39 +0100
categories: ios orientation ux swift
comments: true
---

Now back to mobile development (next to a million other things) at [Kidiyo](kidiyo.com),
I was underwhelmed with the lack of support from the iOS SDK to force different
landscapes across an app. In this blog I share a neat solution that works (at
least) from iOS 9.0 onward.

_TL;DR_: Get [my Orientation extension](https://gist.github.com/NunoAlexandre/a0cd5af9a5c1ea1933baf8a3c6b426f0)
and check [how to use it](#bringing-it-all-together).

Most SO answers suggest to `override` the `preferredInterfaceOrientationForPresentation`,
`supportedInterfaceOrientations` and `shouldAutorotate` variables. These can be
defined on `UIViewController`s and on `UINavigationController`s.

I'd expect to be able to get it working out of the box by simply overriding
`preferredInterfaceOrientationForPresentation` and `supportedInterfaceOrientations`
on my `UIViewController`s and then having `UINavigationController` exposing those
values from the visible view controller.

That setup was insufficient.

### The problem

Let's look at the following scenario.

<img src="/images/rotation-scenario.svg"/>


- A screen `A` _supports_ all orientations but _prefers_ portrait
- A screen `B` only supports landscape
- A user can go from `A` to `B` and back

Setting `B`'s supported orientations to landscape did not force that screen
to be shown in landscape if I'd come from `A` in portrait mode. Instead, I was
kept in portrait in `B` until I rotated to landscape. From then on I no longer
could rotate back to portrait, as intended from the beginning.

Clearly, the SDK didn't take the lead on forcing the orientation despite being
aware what the current view controller supports.

The way to force rotate a screen is by setting the device "orientation" value:

{% highlight swift %}
UIDevice.current.setValue(<UIInterfaceOrientation>.value,
                          forKey: "orientation")
{% endhighlight %}

Forcing rotation is easy, but there are a few caveats to cover.

### Caveats

Note that `preferredInterfaceOrientationForPresentation` is of type
`UIInterfaceOrientation` and that `supportedInterfaceOrientations` is of type
`UIInterfaceOrientationMask`. The second defines the allowed range, while the
first indicates which one, within that range, is the preferred.

Having this in mind, it is now clear that `preferredInterfaceOrientationForPresentation`
is the value to use when forcing rotation.

It would seem like a good idea to always force the orientation to be what the
visible `UIViewController` defined `preferredInterfaceOrientationForPresentation` to be.

This approach works when moving from a screen with a broader set of supported orientations
than the screen we are moving to. For instance, when moving from `A` to `B` in the
scenario above.

Such approach taints the user experience. When at the screen `B`, the user holds
the device in landscape mode. By going back to `A`,  the screen
gets rotated to portrait, even though `A` supports landscape.

### Solution

The solution, then, is to force rotation only when the screen we are moving to does
not support the orientation we are in already.

Unfortunately, `UIInterfaceOrientationMask` is an enum and not a list, so we can't just
check if it contains a `UIInterfaceOrientation`, and it doesn't provide any function
to do this out of the box either.

Therefore, we need to extend it:

{% highlight swift %}
extension UIInterfaceOrientationMask {
  func supports(_ orientation: UIInterfaceOrientation) -> Bool {
    return (orientation.isLandscape && self.contains(.landscape))
        || (orientation.isPortrait && self.contains(.portrait))
  }

  func misses(_ orientation: UIInterfaceOrientation) -> Bool {
    return !supports(orientation)
  }
}
{% endhighlight %}

Now, when moving to a new `UIViewController`, we can check if the current
orientation supports its `preferredInterfaceOrientationForPresentation`. If it
doesn't, we have to rotate to that preferred orientation:

{% highlight swift %}
extension UINavigationController {
  func adjustOrientationIfNeeded() {
   if self.supportedInterfaceOrientations.misses(currentOrientation()) {
     UIDevice.current
       .setValue(self.preferredInterfaceOrientationForPresentation.rawValue,
                 forKey: "orientation")
   }
 }
}
{% endhighlight %}

### Bringing it all together

Now we have everything to have it working like a charm:

- The View Controllers define both their preferred and supported and orientations.
  You can define this in a base class instead of having to define it in every single view controller.
- The capacity to decide when to the screen needs to be rotated

The last step is to have your `UINavigationController` implementing the `willShow`
method from `UINavigationControllerDelegate` calling `adjustOrientationIfNeeded`.

{% highlight swift %}
extension YourNavigationController: UINavigationControllerDelegate {
  func navigationController(_ navigationController: UINavigationController,
                            willShow viewController: UIViewController,
                            animated: Bool)
  {
    adjustOrientationIfNeeded()
  }
}
{% endhighlight %}

Et voilá! You can checkout my whole `Orientation` extension and get done in no time:

<script src="https://gist.github.com/NunoAlexandre/a0cd5af9a5c1ea1933baf8a3c6b426f0.js"></script>
