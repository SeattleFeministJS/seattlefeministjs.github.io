---
layout: post
title:  "React Workshop Two"
date:   2016-10-09
categories: meetup
author: ava
---


## CSS 

My favorite part of web development is CSS, and my favorite way to CSS is to [postcss](http://seattlefeministjs.github.io/postcss), it's no fun to develop anything without some style, so let's add that in right away. Switch to the "css" branch in our repository now, and then run `npm install`. Since we are using webpack we need to process our CSS files with loader plugins, let's look at our `webpack.config.js` first. Even if you're not using postcss you will need the `style-loader` and the `css-loader` at a minimum, you can see we added another test expression for css files below the JavaScript babel loader configuration.

    {
      test: /\.css$/,
      loader: ExtractTextPlugin.extract('style-loader','css-loader?modules&importLoaders=1&localIdentName=[name]__[local]___[hash:base64:5]!postcss-loader')
    }

It is longer expression because we have chained together a plugin, and three loaders, the loaders operate in a right to left fashion. You can see that we have `postcss-loader` as the first in the order of operations, it has to process our variables, mixins and style declarations before moving on the `css-loader` which will process our CSS modules and handle the unique hashing, this works together with `style-loader` which allows us to `import` or `require` our CSS files inside our JS modules, and lastly we call a plugin `extract-text-webpack-plugin` which extracts all our CSS into a single file `styles.css`.

If you look inside the `/src` directory you will see that I have added a new `/components` directory and inside that is a component called "App". This is a pattern I like to use to use as a top level component to add base styles and any other app wide code. Another pattern I like to use is to group components into their own directory that way any other files that support that component specifically can live in the same directory, most often those are CSS files, but there could be images or tests as well. Looking at `App.js` the first thing to notice is in the import section I have imported a stylesheet from the same directory into a variable `s` and you can see that I reference `s` as a `className` on the `div` that is getting returned from our render function. Since "class" is a reserved word in JavaScript, the JSX elements should use the attribute name `className` (the same is true for the "for" attributes on `label` elements instead you must `htmlFor`.) If you look into the `app.css` file you can see that I am using postcss much like SASS with imports and variables, but one really neat thing is that I can also use a JavaScript module as a function to process CSS. If you look at the `variables.css` file you'll see sizes defined for our header tags and they call a function to get define the exact `rem` value. 

    $h1-size: modular-rem($font-ratio, 5);


The function `modular-rem` is living in the `/src/lib` directory, another common pattern for adding helper functions to an application, and is linked to a `postcss-functions` plugin in webpack which tells it which directory to look in for available functions.

I have also added an `animated-gradient.js` to the `lib` which is also imported in the `App.js` file under the stylesheet, but instead of this function being used in CSS we are using it on the DOM to animate the App component background by adding an id to our component and then calling that function once React has loaded the component into the DOM, a process called mounting. This is our first look at a lifecycle event you can see the name of the event is `componentDidMount` which is similar to jQuery's `.ready()` function, this event is called immediately after the first render occurs, which means that the DOM now has a reference to this component to call upon.

    componentDidMount(){
      animatedGradient('animate')
    }

## HTML

Quickly switch to the "html" branch in our repository, and run `npm install`. This is a small thing that I missed last time, but I've added a plugin to webpack called `html-webpack-plugin` and this plugin will generate a final `index.html` for use with our `bundle.js` and `styles.css` added inline, from a template we define in our `src` directory called `index.html` and it works with `webpack-dev-server` to hot module reload as we work. Go ahead and run `npm run bundle` you will now see an `index.html` in the `/dist` directory that you can open up and have it run our CSS example as a compiled static webpage.

### References
<!-- - [Containers and Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.tvxsigg7y) -->
- [Animated Gradient on Codepen](http://codepen.io/13twelve/pen/xLoiH)
- [ClassNames plugin](https://github.com/JedWatson/classnames)
- [Component Lifecycle](https://facebook.github.io/react/docs/component-specs.html)
