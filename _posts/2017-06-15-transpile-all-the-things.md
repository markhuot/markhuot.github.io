---
title: Transpile all the things
excerpt: Adding complexity for the sake of simplicity
---

Writing a whole bunch of `React. createElement` calls gets tiring really quickly. So instead, I'd like to use JSX and just write `<Button/>`. Unfortunately, JSX isn't actually a thing that Jacascript understands or works with. Because of that you need some sort of build step to convert the JSX back in to vanilla JavaScript method names. Personally, I like Babel for this, but there are a few options today if Babel isn't your thing.

There is plenty of controversy around JSX. The cross-mingling of Javascript and HTML is certainly weird. It absolutely goes against my muscle memory of creating a `.js`, `.html`, and `.css` for every component. But, if JSX can push me towards my [requirement]() of truly re-usable components then maybe there's something to it after all.

The thing that you have to accept with JSX (and really React, in general) is that JavaScript is now your point of truth.