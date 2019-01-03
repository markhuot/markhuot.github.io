---
title: Tailwinds and Lobotomized Owl Selector
excerpt: Playing with spacing
---

I love Tailwind CSS. Like, a lot. I know it's not "pure" and that it bloats my HTML but at the end of the day it's the fastest, easiest, most bug free approach to styling that I've found. There's a lot of love (and hate) mail for Tailwind around the web so I won't get in to it too deeply here.

One thing that never sat correctly with me in Tailwind is inter-component spacing. If I'm working on a project and I have something like `media-block.twig` then I would assume that everything in my media block is specific to the media block itself. Unfortunately that's rarely the case. Take the following example,

1. You have a list of blog posts in your main content area
2. You have a short list of "external links" in your sidebar
3. Both of these elements should use the media block component

To do this you could create something like this,

```twig
<a href="#" class="media-block {{ not isFirst ?: 'mt-10' }}">
    <img…
    <p…
</a>
```

And that works well enough, but what if the sidebar needs a smaller margin top, then you've got,

```twig
<a href="#" class="media-block {{ defaultSpace ?: 'mt-10' }} {{ smallSpace ?: 'mt-4' }}">
    <img…
    <p…
</a>
```

Now we can pass in to our component whether it should have a "default" amount of spacing or a "small" amount of spacing. Surly you can see where this is going. Now someone decides to use the media block _inside_ another component and to match this other components spacing we need a third spacing size. Okay…

```twig
{% raw %}
{% set classes = [
    defaultSpace ?: 'mt-10',
    mediumSpace ?: 'mt-6'
    smallSpace ?: 'mt-4',
] %}
{% endraw %}
<a href="#" class="media-block {{ classes|array_filter|join(' ' ) }}">
    <img…
    <p…
</a>
```

Things are dandy until we realize we're using the component in the footer and now we need a fourth spacing value in there… well this could go on forever.

Personally, I try to use the [lobotomized owl selector](https://alistapart.com/article/axiomatic-css-and-lobotomized-owls) for all my spacing needs because it removes the concept of inter-component spacing from the component, itself, and instead applies it to the containing context. The selector looks like this,

```css
.spacing > * + * {
    margin-top: 10rem;
}
```

That says to take any direct descendant (immediately inside the `.spacing` element) that has a previous sibling and apply a `margin-top` to it.

What's so wonderful about this approach is that if you have a list with only one element you won't get any spacing. If you have a list of 10 elements the first element won't get any spacing and the rest will all be evenly spaced out. With this approach your component never needs to worry about it's containing context because the containing context is responsible for its spacing. To use the above `.spacing` class with our `.media-block` it would look something like this,

```html
<div class="spacing">
    <div class="media-block"…
    <div class="media-block"…
    <div class="media-block"…
    <div class="media-block"…
    <div class="media-block"…
</div>
```

That's it. `.media-block` doesn't have to worry about `mt-10` any more.

Within Tailwind we already have nice `margin` values that serve a similar purpose so I built a quick utility to expose the `margin` config as lobotomized owls, You can use it as `.o-{marginKey}`. That means any of the following would work in a default setup of Tailwind,

```css
.o-0 > * + * { margin-top: 0; }
.o-1 > * + * { margin-top: 0.25rem; }
.o-2 > * + * { margin-top: 0.5rem; }
.o-3 > * + * { margin-top: 0.75rem; }
.o-4 > * + * { margin-top: 1rem; }
.o-5 > * + * { margin-top: 1.25rem; }
.o-6 > * + * { margin-top: 1.5rem; }
.o-8 > * + * { margin-top: 2rem; }
.o-10 > * + * { margin-top: 2.5rem; }
.o-12 > * + * { margin-top: 3rem; }
.o-16 > * + * { margin-top: 4rem; }
.o-20 > * + * { margin-top: 5rem; }
.o-24 > * + * { margin-top: 6rem; }
.o-32 > * + * { margin-top: 8rem; }
.o-px > * + * { margin-top: 1px; }
```

You can read more and install it here,

https://www.npmjs.com/package/tailwindcss-owl