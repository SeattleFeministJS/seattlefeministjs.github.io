---
layout: post
title:  "React Workshop Two"
date:   2016-10-09
categories: meetup
authors: ava and jessica
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

## State and Forms

React components, like the Form we're going to create this week, are essentially "state machines". This is like a pattern for making clothes.  You have a pattern, it can accept different sizes and fabrics.  The pattern doesn't care what size or what fabric it gets. It will behave the same in any size or any fabric.  If you make the dress with a flowered courdoroy, or a black satin, the dress, (or the rendered element), will be the same layout and design. In the case of a React component, state is like the dress fabric.  This means that everything flows in one direction - from the parent or wrapper component to the child componenet.  The child accepts props from the parent, and keeps its own internal state, and renders according to the values of the props and state.

Each React component has a state property.  This state property reflects the UI state of the component.  When a user takes action on a form element, the component's state will update.  When the state is updated, using `this.setState()`, the component is re-rendered, this time applying its updated state.  So you can imagine a lot of rendering goes on - if you're updating state every time a text input changes, that's a re-render on every key stroke.

### Create a Form

Let's get started. First, checkout the `forms-1` branch.

Then, checkout commit
```git checkout f3b32f9d2bf370af342856dbe863a828d779dcb5```

You can see that we've created a Form component, imported it in index.js, and created some css rules for it.  Within the form, there is one input, a checkbox.

Try adding a text input or a select element to the form on your own.  If you're able to do that, great!

Next, let's remove any changes and look at the next commit:
```git checkout 418a631c75ff3fec8ea29ca10f844d379d6e1ec1```

You can see that we've added a text input and a select element.  They don't have any event listeners yet, but they will!

### Updating State and User Events

```git checkout 00fcbc7```

Note we are now handling user events on the text input.  What was added in order to update state when typing into the text input?
If we want to similarly handle the event of the checkbox changing on user clicking it, how would we code that?  Give it a try! Add `console.log` statement to check your state before and after the user changes.

`this`
You'll notice that we are using `.bind()` and `this` in the `constructor` function.  Why do we need to do this?  What is the meaning of `this`?

When using ES6 classes in React, it's necessary to bind `this`, meaning the component's context, to each function.  That way, each function can call `this.setState()`.  `this` is a very important part of JavaScript, and different libraries handle assigning `this` in different ways.

Now that we're updating state based on user actions, let's see the almost-final product:

```git checkout 3652e1c```

What's the next step with the form?  Once the user has completed the form, we want to submit it to the backend service that will create a new user. For now, how would you update this form to have a `handleSubmit` function that can log the current state?  Try it out.  Once we're able to log the current state, we can later set up a service to submit the new user using that state.

Try putting a `console.log` statement just before the `return` in the `render()` function.  Then, try filling out and submitting your form.  How often is the component re-rendering?  What values are updating when?

To see the final component, including a `handleSubmit` function:
```git checkout 83cfbac```
This function is "stubbed out", meaning it doesn't really send the data anywhere to update.  This is fine for now, and we added a `TODO` comment, so we can remember to come back later and connect this with our API for creating new users.

### References
- [ES6 Class Syntax in React](https://facebook.github.io/react/docs/reusable-components.html)
- [ES6 Classes in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
