---
title: Transpile all the things
excerpt: Adding complexity for the sake of simplicity
draft: true
---

Writing a whole bunch of `React. createElement` calls gets tiring really quickly. So instead, I'd like to use JSX and write `<Button/>`, which is not only shorter but, IMO, easier to understand as well. Unfortunately, JSX isn't actually a thing that Jacascript understands and, because of that, you need some sort of build step to convert JSX back in to vanilla JavaScript. Personally, I like Babel for this, but there are a few options today if Babel isn't your thing.

There is plenty of controversy around JSX. The cross-mingling of Javascript and HTML is certainly weird. It absolutely goes against my own muscle memory; creating a `.js`, `.html`, and `.css` for every component. But, if JSX can push me towards my [requirement](/2017/06/14/a-n00bs-intro-to-react) of truly re-usable components then maybe there's something to it after all. By co-mingling the HTML and Jacascript it's one less thing to copy between projects and because it's based in JavaScript I have full access to the JS module-based require system.

The thing that you have to accept with JSX (and really React, in general) is that JavaScript is now your point of truth. For a lot of front-end developers the CSS is a point of truth, for good reason. If you're most comfortable in CSS then creating a new Button style would, obvious to you, start in CSS. However, for many JavaScript developers who shy away from CSS, treating JavaScript as that point of truth is obvious. What I like about React is that it sort of straddles both approaches. With components I should create `Button.js` and `Button.css` at the same time. One doesn't really work without the other. And, best of all, trying to add my button to a global `styles.css` or tacking the CSS somewhere it doesn't belong will only make things harder.

So, Jacascript is my truth and I've accepted HTML in my Javascript. Let's move on.

Babel is a `yarn add` away, so let's add it,

```shell
$ yarn add babel babel-plugin-transform-react-jsx
```

My favorite feature of Babel is that it ships with a CLI tool that compiles JSX on the fly. That means that we can run `babel-node index.js` and start working with JSX right away.

As a test I'll update my Button component to use JSX. First, for me, is to rename `Button.js` to `Button.jsx` then, in `MoviePoster.js` I'll update my require statement to `const Button = require('Button.jsx');`. With that out of the way I can start using actual JSX code. That means my render function can be updated like so:

```jsx
const React = require('react');

module.exports = class Button extends React.Component {
  render() {
    return <button>JSX Button</button>;
  }
}
```

To make sure everything is still compiling I can use `babel-node`,

```shell
$ babel-node index.js

```

Excellent, everything actually worked as expected. Now I have a much more terse syntax to define my markup. This should make things much easier to read and, eventually, debug.

Now, like I said last time: HTML in my JavaScript feels wrong on so many levels… but the more I look at the succinct definition of a React component the more I like it. Being able to open one file and see everything there is to know about a button is a powerful process. It'll make on-boarding other developers and refactoring so much easier. Plus: one of my long standing issues with HTML in JavaScript is that you have to shove everything into a string. With JSX you actually have a syntax that can be statically analyzed and linted along side my JavaScript. That's going to reduce a lot of headaches. `/rant` `/justification`

















