---
layout: post
title:  "React Workshop One"
date:   2016-10-09
categories: meetup
author: ava
---


## Ecosystem

Why do you need to have an ecosystem to use React? React is a modular system, and as such it requires that a user of React is not using React all by itself, React is a module that can be used with other modules of your choice to build up a front end application. React is the "view" in your application, other arguably essential modules in your application would include a server (node/express), bundler (Webpack), a router (React Router) and a Store for keeping track of state (Redux). You can choose which modules make the most sense for the application you are trying to build. For the purpose of this workshop you will need to have git, node.js and npm installed and ready to use.


## Using React
Let's jump right into creating a simple component to get a better understanding of what React is. Using our [workshop repository](https://github.com/SeattleFeministJS/React-Workshop) switch to the "simple" branch. We'll start by creating a basic html page that will display a Hello World message. The first thing we need to add is our React library and a library called React DOM. [Why do we need two libraries?](http://stackoverflow.com/questions/34114350/react-vs-reactdom) React, also known as React core, is what gives us the ability to write components and React DOM is the library that renders the components to a specific target, in this case it is the web browser. Originally React was only used for browser implementation and React DOM library was packaged together with core React but since then other rendering targets have been made available, such as [React Native](https://facebook.github.io/react-native/), [React WebGL](https://github.com/ProjectSeptemberInc/gl-react/), recently [React VR](http://www.tomshardware.com/news/web-vr-oculus-facebook-carmel-react-vr,32822.html). Essentially React core is the way we create components and React DOM is the way we put those components into a render target for display. 


We need to have a DOM element which we will mount our React component to in the browser, we've added a `div` with an id of 'app', this should be familiar if you've worked with any other framework or even jQuery. Now we can come to our script block with React code. 

      var div = React.DOM.div
      var h1 = React.DOM.h1

      var HelloWorld = (
        div({style:{color:'purple'}},
          h1(null, 'Hello World')
        )
      )

      ReactDOM.render(HelloWorld, document.getElementById('app'))


First thing to note is that everything is function. You can see we create browser DOM nodes with `React.DOM` API and they map to html elements with the same name. Since our variables `div` and `h1` are actually function expressions for React Dom to execute and render as html tags in the browser, we can pass parameters to them as well. The first parameter is used for passing in attributes to be applied to the DOM element when it renders in a page, as you can see we have a style applied to the `div`, the second parameter is the child of the element, we pass in the `h1` as the child of the div and to the `h1` we pass in a text node.

You have probably noticed that there are parentheses wrapping these function expressions, in React parentheses allows us to write multi line expressions, another thing to note is that our div function expression is the root inside the parentheses, you can only have one single root element in any React component. If you try to add a second element you will get an error.

      var HelloWorld = (
        div({style:{color:'purple'}},
          h1(null, 'Hello World')
        )
        div({style:{color:'purple'}},
          h1(null, 'Hello World')
        )
      )


Finally we call another function from the React DOM library to render the node into the html page at a mount point which have defined as the div with the 'app' id.

    ReactDOM.render(HelloWorld, document.getElementById('app'))


## Components

So now that we have a super basic implementation working we realize this is not how anyone would actually use React, so let's start building up our app up into modular components that we can reuse, go ahead and switch to the 'class' branch in our demo repository. We start by pulling out our JS code into a a separate file, let's start organize our code into a directory structure that we'll continue to use as it grows. Here we created a `src` directory where our components will go, and defined the entry point of our application as `index.js`.

Let's update or code to add a component called "Title" which can be reused, we do this by creating a ["class"](https://en.wikipedia.org/wiki/Class_(computer_programming)). I don't want to get to deep into the semantics of object oriented languages and how JavaScript is a prototypal language that doesn't have true classes, only to say that the intention of the React library method `createClass` is to create encapsulations with embedded state. A [`ReactComponent` Class](https://facebook.github.io/react/docs/glossary.html#react-components) is simply a JavaScript class (or "constructor function"). When this constructor is called it will return an object with it's own internal methods, which we will use to render and hook into lifecycle events, this return object is what is referred to as a `ReactComponent`

    var Title = React.createClass({
      render() {
        return (
          div(null,
            h1(null, 'Hello World Class')
          )
        )
      }
    })




## Elements

So now we have moved our HelloWorld function into a reusable class, we need to now tell React how to instantiate that class and render it into the DOM,  we do this by using `createElement`. ReactElement is a virtual ReactComponent before it gets constructed. An old and new ReactElement can be compared to see if a new ReactComponent instance should be created or if the existing one should be reused. This is how the React DOM "diffing" features work.

The render method of a ReactComponent is expected to return another ReactElement. This allows these components to be composed. Ultimately the render resolves into ReactElement with a string tag which instantiates a DOM Element instance and inserts it into the document.

    var HelloWorld = (
      div( null,
        React.createElement(Title, null)
      )
    )

    ReactDOM.render(HelloWorld, document.getElementById('app'))


## Props

Props allow us to pass things into our components making them much more reusable. In this case we are passing a string to display inside the `h1` tag but props can pass more than strings, it can pass objects and since functions are objects in JavaScript we can pass functions as well, and now you can begin to see how flexible components can be when using props. We will dig into props in more detail later on, for now it's good enough to understand that they exist and they are used to customize components.


## JSX
Now that we have seen how React is rendering classes to a page and how to create reusable components we can look at what the React documentation, and alot of other learning resources start with from the beginning and that is called `JSX`. Facebook recommends using `JSX` because it is concise and has a more familiar syntax which is friendlier to use. For example if we were to use `JSX` in our example above we could simply write something like

    var HelloWorld = (<div><Title title="Hello World"/></div>)


The catch is that you have to use some tooling to transform `JSX` code into plain old JavaScript objects, as we have written above. This is where alot of beginners begin to struggle, as most examples are incomplete or point you to a bloated boilerplate with everything possible crammed in there and no explanation of how it all comes together and why you need to use these tools in the first place. This is the "ecosystem" and it involves using a transpiler called Babel, and the most common way to use Babel and React is with a module bundler, which is where Webpack comes in (Webpack isn't the only option but it is the one most commonly used today.)

## Babel

Babel is library that originally was called "6 to 5" because it's main purpose was to "transpile" modern JavaScript, at the time there was only ES6, to JavaScript that a browser could understand, which is called ES5. Now we have ES7 and JSX that we would like to use in our source code, and so the name change seemed to better suite the growth of the library which is to interpret different types of syntax to ES5. Babel, much like React is a modular system, meaning that you have to hand roll you tools to fit your specific use cases using plugins. Switch to the "babel" branch in our repository now, and then run `npm install`. You can see in the `package.json` that we have installed `babel-core`, `babel-preset-es2015` and `babel-react` as development dependencies. There is a way to run babel from command line but we are going to be using with it Webpack so we won't be going into detail about `babel-cli`, but if you are using with node or for command line you will need to add a configuration file where you define the required plugins, called presets, named `.babelrc`.   

## Webpack

Webpack is what brings together all the fun new JavaScript code into a single bundle to be loaded and run in a browser and also provides a nice [development server](https://webpack.github.io/docs/webpack-dev-server.html) which will watch and reload files for you as you edit. Switch to the "webpack" branch now, and run `npm install`. Webpack, being a module bundler, is also a modular system of plugins, called [loaders](https://webpack.github.io/docs/using-loaders.html), which you add to your project to support your needs. Webpack works very similarly to Gulp in that it processes files that are defined in a regular expression and applies transformations to the code in a stream and will output the result of those transformations to a destination defined in a configuration file. The configuration file should be named `webpack.config.js` because that is what webpack will look for in the project root directory by default. You can name it a different name, or keep it in a different location if you want but you have to specify where it is what is named if you do.

Looking inside the Webpack configuration file we can see that we set some variables defining the project root and the asset path, this is the in and out points which Webpack will be working with, we also `require` the Webpack library using node module require statement. We then create a module to export, which is the configuration object. Our configuration is pretty simple at this point and this is what most boilerplate projects lack, simplicity. Webpack can do many kinds of complex transformations to our source files, and will be building on this as we go, but if you are trying to learn you don't want a full configuration file to reference because as mentioned, it can get pretty complex and it's very easy to configure one thing incorrectly and the whole setup becomes unusable. Another source of frustration, in my opinion, is the Webpack documentation, it is sparse and to me it assumes the reader has all this background knowledge of all the possible scenarios and knows which things are required to get started and which things can be left out for their project. So even though the configuration has documentation for each entry in this file we'll go over each one here.

`context` - This tells Webpack where to start looking for it's source files, it the root of the project.

`entry` - This is the entry point to your application, and can be different from the root of your project, in our case our source files all live in `/src`, and the entry point into our application is the `index.js` file. It is possible to have multiple entry points, but that goes into more advanced usage of Webpack and we will not be going into how that works in these workshops.

`output` - This is a configuration object for how and where Webpack will be writing it's output files, "the bundle". The `path` is self explanatory, the path to output files, the `filename` also self explanatory, the name of the file to be output, and the `publicPath` is the path which the development server will be outputting a bundle 'in memory' while we are developing. If you look into the `index.html` inside the `src` directory you will see that we have added a script tag with source set the same location as out output config `dist/bundle.js` and another script tag with the source set to `http://localhost:8080/webpack-dev-server.js`, this is what allows the development server to do what is called "Hot Module Replacement", which is what updates the browser whenever you make a change to the source code.

`resolve` - This tells Webpack about the code it should be processing. `modules` tells Webpack which directories to look for modules that are going to used in the application and `extensions` tells it which files to look include from the the modules.

`stats` - Is logging, `colors` is for formatting output using colors `reasons` will print out the exceptions and files that caused them. `chunks` will report when code chinks have been processed and what the output is from that processing.

`module` - Options applied to the modules being processed, `loaders` is an array of loaders that will be applied to files within modules, files are tested against a regular expression for extension and if they pass the loader will process those files.

## NPM Scripts

Take look inside the `package.json` and at the top you will see a block called "scripts". This area allows you to define commands that you would run frequently, or in use in an automated build environment. There should be two commands there for executing webpack, the first is for running a live reload development server and the second is for creating a static bundle. To use the scripts you only need to type `npm run <command name>`, so for development go ahead and run `npm run dev` you should see the console output the stats that we defined and you should be able to open your browser to [http://localhost:8080](http://localhost:8080/) to see the app running. To kill that process in the command line you would hit `ctrl-c`. Go ahead and run the bundle command, `npm run bundle`, this should output the static compiled bundle file to the `/dist` directory that we defined in the webpack config. Go ahead an open up the bundle file that was generated in `/dist/bundle.js`, you can see at the top some inserted code required for bootstrapping webpack in the browser and below that is the component code. It's nice to look at this with an incredibly simple example such as the one we have because it isn't confusing, you can clearly see webpack code followed by your code, as you app grows so will the bundle, which we see when add in modules.


## Modules

We've been saying all this stuff is modular, and it isn't until now that we can actually start writing and requiring modules with ease. Switch to the "modules" branch now and run `npm install`. Since we have Webpack setup to bundle our modules for the browser we no longer need to use a global script include for React and React Dom in our `index.html`, in general globals are bad, they can cause problems if there are two libraries with the same name, or using the same global variables names. Without React being globally available we have to now include them wherever they are used. If you look into the `index.js` you can see now that we have added vars for both `React` and `ReactDOM`, we have also moved the Title component into it's own file and required that using a dot slash syntax, indicating to Webpack that this is a local file and not an NPM module, so it knows to look for modules without any special pathing inside the `node_modules` directory at the root of your project. Go ahead and run that bundle command again and inspect the generated `bundle.js`, look how much it has grown since the last time we looked, this is way more confusing to me now because it has bundled together React, React DOM and all their dependencies along with our simple little component. 


## ES6

Now that we have Babel processing all of our files through Webpack we can use some newer ES6 syntax. Switch the "es6" branch now.  If you look into the `index.js` file now you can see that we have replace the `require` syntax with ES6 module syntax. 

  `var React = require('react')`

  becomes 

  `import React from 'react'`

  And if you look into the `title.js` file you will see a more interesting way to import modules called [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment), this is how to import named exports from a larger scope into their own variables in a single line. You may also notice that we have used ES6 module export syntax to export our Title module and we have forgone manually creating a React class to create our composite component and use their [Top Level API](https://facebook.github.io/react/docs/top-level-api.html) for `Component`.

      import React, { Component } from 'react'

      export default class Title extends Component {
        render() {
          return (
            <div><h1>{this.props.title}</h1></div>
           )
        }
      }



Next time we will get more into the React API's and start creating modules that manage state, which is where most people want to start from day one and then they quickly become frustrated because there are many disparate moving parts that need to be in place before you can really start coding some interesting stuff. Now we have the foundation set and a clean simple boilerplate with which we can continue to build upon and experiment until we meet again!


### References
- [Github repository for this workshop](https://github.com/SeattleFeministJS/React-Workshop)
- [React Documentation ](https://facebook.github.io/react/)
- [React Enlightenment](https://www.reactenlightenment.com/what-is-react.html)
- [JSX In Depth](https://facebook.github.io/react/docs/jsx-in-depth.html)
- [Babel](https://babeljs.io/)
- [Webpack](https://webpack.github.io/docs/)
