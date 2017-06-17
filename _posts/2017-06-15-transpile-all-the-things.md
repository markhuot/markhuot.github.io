---
title: Transpile all the things
excerpt: Adding complexity for the sake of simplicity
---

Writing a whole bunch of `React. createElement` calls gets tiring really quickly. So instead, I'd like to use JSX and just write `<Button/>`. Unfortunately, JSX isn't actually a thing that Jacascript understands. Because of that, you need some sort of build step to convert the JSX back in to vanilla JavaScript method names. Personally, I like Babel for this, but there are a few options today if Babel isn't your thing.

There is plenty of controversy around JSX. The cross-mingling of Javascript and HTML is certainly weird. It absolutely goes against years of muscle memory; creating a `.js`, `.html`, and `.css` for every component. But, if JSX can push me towards my [requirement](/2017/06/14/a-n00bs-intro-to-react) of truly re-usable components then maybe there's something to it after all.

The thing that you have to accept with JSX (and really React, in general) is that JavaScript is now your point of truth. For a lot of front-end developers the CSS is a point of truth, for good reason. If you're most comfortable in CSS then creating a new Button style would, obvious to you, start in CSS. However, for many JavaScript developers who shy away from CSS, treating JavaScript as that point of truth is obvious. What I like about React is that it sort of straddles both approaches. With components I should create `Button.js` and `Button.css` at the same time. One doesn't really work without the other. And, best of all, trying to add my button to a global `styles.css` or tacking the CSS somewhere it doesn't belong will only make things harder.

So, Jacascript is my truth and I've accepted HTML in my Javascript. Let's move on.

Babel is a `yarn add` away, so let's add it,

```shell
$ yarn add babel babel-preset-env babel-plugin-transform-react-jsx
```








