---
title: Vertical Spacing
---

I've learned to treat my headings pretty abstractely. I don't add a ton of styles on a naked `h2`, for example. Instead I put my styles on a classed `.beta` element so that I can re-use that style on a `p` or a `strong` element if the need arises. This works great and has really helped me keep my front-end as flexible as possible. Where it breaks down, for me, is when I start applying some vertical rhythm to the page. Sometimes I need an `h2` inside a module to have no vertical margin because it has a bottom border, or some background box around it. In these cases I find myself overriding the defauts with junk like,

{% highlight scss %}
.beta {
  font-size: 48px;
  margin-bottom: 36px /* 0.75em */;
}
.beta-collapsed {
  margin-bottom: 0;
}
{% endhighlight %}

That gets a lot worse once you add breakpoints,

{% highlight scss %}
.beta {
  font-size: 48px;
  margin-bottom: 15px;
  @include breakpoint($large) {
    margin-bottom: 36px /* 0.75em */;
  }
}
.beta-collapsed {
  margin-bottom: 0;
  @include breakpoint($large) {
    margin-bottom: 0 /* specificity override */;
  }
}
{% endhighlight %}

Gross, right? Well, stealing a little bit from SMACSS I've been exploring using unique classes for my vertical spacing too. I'm not sure what to name them yet, but I'm thinking something along the lines of,

{% highlight scss %}
.vspace-1 {
  margin-bottom: 25px;
  @include breakpoint($large) {
    margin-bottom: 50px;
  }
}
.vspace-2 {
  margin-bottom: 15px;
  @include breakpoint($large) {
    margin-bottom: 30px;
  }
}
{% endhighlight %}

Now I can do things like,

{% highlight html %}
<h2 class="beta vspace-2">...</h2>
<div class="hero vspace-1">...</div>
{% endhighlight %}

Now I have the flexibilty to re-use my hero and beta elements wherever I need to without having to override anything.
