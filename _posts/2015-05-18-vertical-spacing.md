At Happy Cog we treat our headings very abstractely. We don't add a ton of styles on a naked `h2`, for example. Instead we put our styles on a classed `.beta` element so that we can re-use that style on a `P` or a `strong` element if the need arises. This works great and has really helped us keep our front-end as flexiable as we need it to be. Where it breaks down for me is when we start applying some vertical rythym to our page. Sometimes we need an `H2` inside a module to have no vertical margin because it's got a bottom border, or some background box around it. In these cases I find myself overriding the defauts with junk like,

```css
.beta {
  font-size: 48px;
  margin-bottom: 36px /* 0.75em */;
}
.beta-collapsed {
  margin-bottom: 0;
}
```

That gets a lot worse once you add breakpoints,

```css
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
```

Gross, right? Well, stealing a little bit from SMACSS I've been exploring using unique classes for my vertical spacing too. I'm not sure what to name them yet, but I'm thinking something along the lines of,

```css
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
```

Now I can do things like,

```html
<h2 class="beta vspace-2>...</h2>
<div class="hero vspace-1>...</div>
```

Now I have the flexibilty to re-use my hero and beta elements wherever I need to without having to override anything.
