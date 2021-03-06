---
layout: post
title: "What's the Best Way to Render a Game in JavaScript?"
categories: games
tags: [gamedev,games]
image:
  feature:
  teaser:
  credit:
  creditlink: ""
---

One of the most challenging parts about making a web game is understanding the technologies you can use to render to the browser. Our game, nibbly.io, is constructed from [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG) assets being drawn to the [Canvas](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API). We use SVG assets because we want the art assets to scale to any screen resolution, which was important to us because we have players with wildly different monitors viewing our game. Some games (like agar.io) just use the Canvas API to draw the entities (eg. the simple blobs) in their game, but we wanted to have a focus on detailed, collectible creatures, for which the Canvas API would be hard to use. Canvas wasn't always how we were trying to render our game, but it ended up being better than directly using SVG tags.

We tried out a large number of solutions, so let's go through them one by one.

**[two.js](https://two.js.org/)**: We started out with two.js, a library with a symbolic connection to the premier WebGL library, [three.js](https://threejs.org/). Our past experience with three.js made using two.js easier, and it has a nice API. More than any other Canvas library, two.js is ambitious: it tries to wrap WebGL, SVG, and Canvas APIs and treat them all as the same thing. It's really cool, but it just wasn't fast enough for what we were trying to do. It could end up being very useful for art projects, though, and we're keeping an eye on it. Big thanks to the maintainer, [jonobr1](https://github.com/jonobr1), for helping us early on, too.

**[fabric.js](http://fabricjs.com/)**: FabricJS was our next stop in the JavaScript rendering framework world, and it was a really good option for a really long time. It has excellent documentation, and an easy-to-use API. We started getting closer to a fully-featured game, though, and found that even FabricJS wasn't fast enough for what we needed to do. We also had some problems with text rendering, and compatibility with Firefox. It's a great library, but to make a 60fps game is actually really hard, and it just isn't quite made for that.

**[d3.js](https://d3js.org/)**: After FabricJS, we asked ourselves: if we're using SVGs for our assets, what if we just directly used the SVG tags that you can embed into HTML? As such, we set out to use D3.js. D3.js is really cool for other things, but honestly, this just isn't the best idea. When you have food, creatures, and projectiles on screen all at once, you get tons of [HTML reflow problems](https://developers.google.com/speed/articles/reflow). We're happy to have played around with D3, though, and would gladly use it for graphing data.

**[Elm SVG](http://package.elm-lang.org/packages/elm-lang/svg/latest)**: How do you get around reflow issues? A [virtual DOM](http://reactkungfu.com/2015/10/the-difference-between-virtual-dom-and-dom/)! And why use React when you have a penchant for functional programming and this new hot language, [Elm](http://elm-lang.org/), is supposed to be [super fast](http://elm-lang.org/blog/blazing-fast-html-round-two)? Off we went with Elm, and let me tell you: Elm is awesome. Elm is everything JavaScript isn't, and that's a pretty awesome thing to be. Elm also *really is* super fast. The problem was, though, that SVG rendering for games just isn't a good idea, and even Elm couldn't save us from that. We didn't have reflow issues, but you don't have much control over how things are actually drawn, and as it turned out, Firefox just wasn't handling rendering SVGs as well as Chrome and there wasn't anything we could do about it. We ended up using Elm for our user experience (basically everything other than rendering the game), but we moved on from using *elm-svg* as our rendering strategy.

**Directly using the [Canvas API](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)**: Here's something you find often in computer science (and maybe in life in general, too): sometimes the most boring thing to do is the right thing to do. We shed all the frameworks and went straight to using the Canvas API. Once you start down this path, there are actually some major decisions to make, so it wasn't over yet. We started using our SVGs with the Canvas API by setting the SVG up as an [Image()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/Image) and then drawing it with [.drawImage()](https://developer.mozilla.org/en/docs/Web/API/CanvasRenderingContext2D/drawImage). This was better than all previous solutions. This also wasn't good enough, yet. Frames were rendering in 8-11 ms when things got complicated, and we were looking for [somewhere around 1-2 ms](https://www.paulirish.com/2011/requestanimationframe-for-smart-animating/). 

So, we pre-rendered all the SVG assets to a big map of hidden Canvas objects, and then rendered those Canvases to the main game canvas. Phew! And that worked wonders, clocking in at 1-2ms on our test machines (and hopefully on your machines at home, too).

That's our whole story, so far! For the future, we're looking at the [elm-canvas library](https://github.com/Elm-Canvas/elm-canvas) as a potential option. It would allow us to merge our UX (made in Elm) with our Canvas code (made in JavaScript), and the folks working on it seem serious about performance and doing things the right way.

If you want to see how our high-speed rendering efforts went, head back to the [nibbly.io home page](http://nibbly.io) to give it a shot.