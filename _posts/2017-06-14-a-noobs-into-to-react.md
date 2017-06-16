---
title: A n00bs intro to React
excerpt: Diving in head first to React.js
---

Confession: I've wanted to learn React for some time. I've been looking for the right project to dive head first into React and it just hasn't happened yet. I've been using Drupal and CraftCMS for my back-and-front-end needs without issue for a while now and I couldn't justify the additional technical overhead of a Node front-end to really make it worth it. So, I've decided to stop waiting and just dive in anyway. This is my personal log of the journey from React n00b to something slightly better than a React n00b…

## Requirements

Like any good developer: I usually start writing code and worry about requirements later. Still, here's some swirling thoughts that guided my initial work,

1. The end result must work with out Javascript. This means that I'll have to use server side rendering. Ideally, I'll use some form of progressive enhancement that renders a stub of a page to baseline users and enhances it with Javascript after the initial render. There are a few different reasons for this requirement but most broadly it's just the right thing to do. SEO needs HTML to crawl (sure, sometimes JS is executed, but sometimes it's not…). Crummy mobile or public hot spots are slow as molasses and waiting for the entire React lib to download is just painful. Also, I don't want to deal with all the janky JS-based routing libs out there just to see content on the page.
2. I love me some ES6 + Babel + Webpack or whatever the latest hotness is today. Shiny isn't always good but I see a lot of benefit to using native Node modules/require statements in my code. Typescript is an obvious next step and I may or may not go down that road, but I'll start without.
3. CSS should be a part of this. I'm not exactly sure what that means yet, but I do know that I want to be able to bundle my CSS up with my Javascript modules so it's composable with everything else. This probably means something like css-modules but, again, we'll see.
4. Everything should be re-usable. For too long I've been writing CSS (Less, Sass, etc…) that compiles to CSS that I then need to copy/past over to other projects. I've tried using Bower to improve this, but I still end up (somewhere) copying HTML or class names out of sample files into my actual project. I'd like to avoid all that and be able to publish a bunch of HTML to NPM (or a private registry) and simply `require` it the next time it's needed.

## yarn init

With that out of the way it was time to dive in. The first step was to set up the basis of my pretend project. I could have used any number of boilerplates out there but I wanted to start fresh and understand the roots before I selected something off the shelf. To that end I started as simply as possible, an empty folder: `mkdir my-great-project`.

With nothing to start from I had to set up my package manager and require the base libs myself. This was done with,

```shell
cd Sites
mkdir my-great-project
cd my-great-project
yarn init
yarn add react react-dom
```

I know I need `react` and if I've been reading tutorials correctly I need `react-dom` to make it appear on the page. I guess I could swap `react-dom` with some other renderer if I wanted to go `react-native` or something like that. ¯\_(ツ)_/¯

## Do something

At this point I think I can use regular old React to make something happen. React works with components and the most basic component I could think of was a button component so I added `Button.js` with some boilerplate React code…

```javascript
const React = require('react');

module.exports = class Button extends React.Component {
  render() {
    return '<button>Hi</button>';
  }
}
```

Great, next up I want to see my button do something so,

```shell
$ node Button.js
$
```

A whole lot of nothing, because my `Button` class doesn't actually do anything. It just sets up a reusable component that my application can use as it sees fit. So, not what I wanted, but this is still sort of success, right? I now have a format that I can write my HTML in that can be re-used and (more importantly) `require`-d later on in any number of projects.

At this point, my Button is a stateless “component” (in React lingo). I need to make it do something but at this point I don't even know what that means. I guess “do something” means render my button out to a page. But in order to see all this in a browser I'll need to create a web server that can accept requests from a browser, compile and render React code, and then return a status code with that HTML. That's crazy talk for where I am. Right now I'll settle with just rendering out my `Button` to the console.

## Rendering

React is, for the most part, a front-end framework. I'd certainly consider it a front-end oriented framework. Because of this leaning, the limited code I've written so far doesn't do much on the server (or in the terminal) so I'll need to add to it. `react-dom` handles this for me I think. Specifically, `react-dom/server` is the module that seems to take a react element and render it out to a string that can then be returned to a browser. I could add some `react-dom/server` code to `Button.js` but then I wouldn't have a stateless component any more. I'd have some hodgepodge mix of front-and-back-end code all mashed into `Button.js`. Instead, I'll do like my front-end self wants and make an `index.js` that will do the work for me. Within my index I'll want to load up my re-usable button and render it out to the “page” which, right now, is just the terminal. So, `index.js` needs some require statements up top to load everything in and then a call to the render function.

```javascript
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const Button = require('./Button.js');

ReactDOMServer.renderToString(Button);
```

That seems to make sense to me, I'm asking it to render Button, which is the “component” I just created above. If everything goes well I'll have `<button>Hi</button>` printed to the screen.

```shell
$ node index.js
Invariant Violation: renderToString(): You must pass a valid ReactElement.
```

Sigh, not so much. Okay, so you can't just render `Button` because it's just a class and `renderToString` expects the instantiation of the class is what I'm understanding this to mean. So maybe `new Button`? Nope, not that either.

Digging deeper into some React documentation I see a lot of `ReactDOM.render(<Button/>)` which is only slightly different that what I have above. But, the `<Button/>` argument there is actually `.jsx`, not `.js` so that means I'll need a compiler, like Babel, to do this. I'm not there yet and I'd really like to see my Button do something before I start adding in more complexity, like Babel or Webpack.

Luckily, JSX is just some syntactic sugar on top of regular Javascript. The JSX of `<Button/>` is the same as `React.createElement(Button)`. In fact, for all the JSX demos out there I could manually transpire those all back to JS by just swapping the angle brackets for `React.createElement`. It's gross, but it would work.

Okay, back to my—what is this that I'm making again, a prototype?—code, I'll update the JSX to JS,

```javascript
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const Button = require('./Button.js');

ReactDOMServer.renderToString(React.createElement(Button));
```

And then running it gives me,

```shell
$ node index.js
Invariant Violation: Button.render(): A valid React element (or null) must be returned. You may have returned undefined, an array or some other invalid object.
```

Well, some progress, it seems that we're past the `renderToString` error and our new error is actually in the `Button.js` component. Which means everything is working! Sort of.

It looks like my Button component needs to return a React element, not a string… Well, a button is an element, I could update `Button.js` the same way I did `index.js`…

```javascript
const React = require('react');

module.exports = class Button extends React.Component {
  render() {
    return React.createElement('button', {}, 'hi');
  }
}
```

And running that,

```shell
$ node index.js 
$
```

Hrm, no errors but no output either… I'll look at `index.js` again and realize that `ReactDOMServer.renderToString` isn't actually doing anything. It's rendering my component to a string and then stopping. If I want to take the return of the `renderToString` and show it in my console I'll need to `console.log` it, at the very least.

Updating index.js to,

```javascript
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const Button = require('./Button.js');

console.log(ReactDOMServer.renderToString(React.createElement(Button)));
```

And running that,

```shell
$ node index.js 
<button data-reactroot="" data-reactid="1" data-react-checksum="314905309">hi</button>
```

🎉 Success! Markup from React into my console. Sure, this is easy for someone who's worked with React every day. For me this is a victory though. Who knows what `data-reactroot` is or why I have a `data-react-checksum` on my HTML. That's a problem for another day.

Okay… maybe today, it's only 2am and this is fun!

## Nesting

I wanted to make this a little more involved and needed something to work against. I searched Dribble for “widget” and found this movie poster that could probably be “React-ified" without too much trouble.

https://dribbble.com/shots/2197748-Day-049-Movie-Card

In there, I've got a few “components” to worry about:

1. A “photo” component for JGL's smiling face
2. Maybe a “movie details” component for the text
3. A “rating” component for the 7.5/10?
4. A “button” component for the “Watch Trailer” link

So, let's set them all up and see what happens. First thing's first, let's create our `MoviePoster.js` component,

```javascript
const React = require('react');

module.exports = class MoviePoster extends React.Component {
  render() {
    return React.createElement(…
```

Hrm… my first issue: the movie poster doesn't really do anything. It's sort of just a container for all the inner components. But, if I've learned anything from my React reading it's that every component must have one (and only one) root element. I guess we'll make that a `div`. With that decided we'll need to make our inner components next, `Photo.js`, `MovieDetails.js`, `Rating.js`, and our existing `Button.js`. Each of these could have some placeholder like this for now,

```javascript
const React = require('react');

module.exports = class MovieDetails extends React.Component {
  render() {
    return React.createElement('div', {}, 'MovieDetils');
  }
}
```

With those created we can update our `MoviePoster.js` to include each of the sub-components.

```javascript
const React = require('react');

module.exports = class MoviePoster extends React.Component {
  render() {
    return React.createElement(
      'div',
      {className: “movie-poster”},
      React.createElement(Photo),
      React.createElement(MovieDetails),
      React.createElement(Rating),
      React.createElement(Button)
    );
  }
}
```

More `React.createElement` here because, again, I can't pass `Photo` to React, I need to turn it into an actual “DOM” element for it to do anything. This is getting a bit tedious and probably means it's time for some JSX. But, before going there let's adjust `index.js` to point to our `MoviePoster.js` and see what happens. `index.js` now looks like this,

```javascript
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const MoviePoster = require('./MoviePoster.js');

console.log(ReactDOMServer.renderToString(React.createElement(MoviePoster)));
```

Running `index.js` gives us,

```shell
<div data-reactroot="" data-reactid="1" data-react-checksum="-447862399"><div data-reactid="2">Photo</div><div data-reactid="3">MovieDetails</div><div data-reactid=“4”>Rating</div><button data-reactid=“5”>hi</button></div>
```

That's each of the pieces all rendering out to the console! But, before I go any further it's time to drop all that `React.createElement` noise for some JSX.

—

Note: this is a journal with no timeline, budget, or strategy. If you enjoyed reading along please let me know. If you have questions or comments, please leave them here and I'll do my best to respond.
