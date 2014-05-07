---
title: Grid Systems
---

Grid systems are hard, or at least they seem to be based on the complexity of the ones out there in the wild. They offer multiple layout systems, gutter sizing methods, and a plethora of mixins to tie them all together. While I see the advantge of all these features, most of the time I'm just looking for a simple way to lay a few things out in a single row. Enter Ra,

* https://github.com/markhuot/ra

Ra is just two mixins that I've used in the past to lay out a basic grid. It's power is in its simplicity. Add the `row()` mixin to the parent of your rows. Then, add the `col($width)` mixin to the children you want to become your columns. Specify a width by passing a percentage to the mixin, say `1/4` to use four columns. That's it. Maybe some day I'll add push and pull methods or swapping source order. But, for now, it's just an easy way to set rows and columns.

On to the example,

```scss
.main {
  @include row();
}
.nav {
  @include col(1/3);
}
.content {
  @include col(2/3);
}
```

With the above we've got a `div.main` that represents our row and then two columns inside. The `.nav` column is narrower, taking up just 1/3 of the width. The `.content` div is larger, taking up 2/3 of the width. Need padding or gutters? Just add it to the CSS, Ra doesn't mess with the spacing in any way.