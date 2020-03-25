---
title: Single-file Web Components
excerpt: 'HTML or Javascript, you decide'
---
I've long been a fan of Vue's HTML-based single-file components. They feel natural and basic (in a good way). I really like being able to swap languages via a simple attribute.

```html
<style language="sass">
```

And for beginners the whole thing just feels really approachable. Look at a starter Vue component. Most people could dive in to that with a basic understanding of HTML.

```html
<template></template>
<style></style>
<script></script>
```

BUT (and there's always a but), I read a comment about Javascript-based components the other day that has started to resonate with me.

> I've been on the Polymer team, making web component libraries, for a long time. And while we used to promote "single file" style authoring in HTML files, we've come around (me very strongly) to the idea that it's better to do single-file components in JavaScript.
- [source](https://news.ycombinator.com/item?id=22666536)

This is one of my struggles with the Vue approach. It seems super verbose to me to have to `import` components in my `<script>` tag and then expose them via a `components` prop just so I can use them in my `<template>`…

This reasoning is exactly what I've been struggling with,

>Templates need some kind of expression language. If the template is in HTML then you need to invent expression delimiters, the expression syntax and semantics, and a way to get data into HTML: HTML doesn't natively have a way to refer to JavaScript objects. This is all custom, needs code to implement, and you need to train developers on it.
>
>On the other hand if you write templates in JS you can take advantage of full JavaScript expressions, imports, and natural lexical scoping... and the data is typically already in JavaScript.

Exactly. Remembering `v-if` or `{% raw %}{% for item in array %}{% endraw %}` is extra overhead to the JS I'm already writing. Keeping components in JS definitely increases the learning curve but also exposes so much more functionality to the component than is possible trying to write some facsimilie of HTML.
