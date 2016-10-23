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

## Components

We've already used components but we didn't get in depth about the internals of a component yet. So far we know that a component is a function, it can also be an ES6 class, and that it comes with a `render` method to attach the component to the DOM. We've also looked at the ability to pass in props and attributes and how that makes components highly extendedable. So far we have built "stateless components", they accept some properties, render to the page and never change. To add state to a component we have built in methods to help us set and track state changes when extend the `React.Component` class. Some are provided by the JavaScript language, such as `constructor` `super` and `this`, and some are provided by React, such as `state`, `setState` and the Component ["Lifecycle Events."](#lifecycle-events)

## DOM Diffing

The React Lifecycle is what happens when the React creates ["ReactElements"](http://seattlefeministjs.github.io/React-Workshop-One#elements) to be rendered into the DOM. Each time React executes the `render` function it creates a virtual tree, or virtual DOM, of components and keeps that object in memory before attaching it to the browser DOM. When there is a change in state React will call again on the `render` function returning a new tree of elements but this time before attaching it to the browser DOM it will compare the new virtual DOM against the saved in memory reference to the current in browser DOM to decide which browser DOM elements need to have updates applied and how to apply them. This is called "Reconciliation" and is achieved with a "Diffing Algorithm" that compares root elements to determine how to process the changes. If two root elements are of the same type React will compare the props and attributes for changes and keeps the same DOM node, but if the elements are of a different type React will create a completely new DOM node. When a React Component changes the instance stays the same, maintaining it's state across multiple renders.

## Lifecycle Events
Lifecycle events provide opportunities to hook into the reconciliation process to apply logic and determine the component state changes before and after they have been added to the DOM.They execute in the following order, with render being called twice to illustrate a state change.

 - [**`componentWillMount()`**](https://facebook.github.io/react/docs/react-component.html#componentwillmount){:target="_blank"} - Called *before* `render`, you can update state without triggering a re-render and it only gets called once, the first time the component gets mounted into the DOM.
 
 - [**`render()`**](https://facebook.github.io/react/docs/react-component.html#render){:target="_blank"} - The only required method on a React Component, it returns a React Element, `null` or `false`.

 - [**`componentDidMount()`**](https://facebook.github.io/react/docs/react-component.html#componentdidmount){:target="_blank"} - Called once, immediately after the component has been mounted onto the DOM, you can update state here but it will trigger a re-render. This is a good place to put any asynchronous calls or any functions that require a global reference, such as `window`.
 
 - [**`componentWillReceiveProps()`**](https://facebook.github.io/react/docs/react-component.html#componentwillreceiveprops){:target="_blank"} - Called before a component receives new props, it can be called any time React detects a change in state and is a place to update internal component state in response to incoming prop changes.
 
 - [**`shouldComponentUpdate()`**](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate){:target="_blank"} - Called before rendering when new props have been detected, returns a `boolean` and is defaulted to `true`, this is a possible place to optimize application performance but generally should only be used if there is a special case to do so.
 
 - [**`componentWillUpdate()`**](https://facebook.github.io/react/docs/react-component.html#componentwillupdate){:target="_blank"} - Called immediately before a re-render, it is not called on the initial render, you cannot set state here, you can call or update internal class methods and properties attached to the `this` property.


 - [**`render()`**](https://facebook.github.io/react/docs/react-component.html#render){:target="_blank"} - Returns React Element, `null` or `false` based on the evaluation of the events preceding it's invocation.


 - [**`componentDidUpdate()`**](https://facebook.github.io/react/docs/react-component.html#componentdidupdate){:target="_blank"} - Called immediately after an update has been applied, it is not called on the initial render, but is similar to `componentDidMount()` in that you can update state here but it will trigger a re-render and it is a good place to put any asynchronous calls or any functions that require a global reference.


 - [**`componentWillUnmount()`**](https://facebook.github.io/react/docs/react-component.html#componentwillunmount){:target="_blank"} - Called just before a component is taken out of the DOM and destroyed from memory, this is where you would perform any cleanup of created DOM nodes, timers or asynchronous operations in progress.


## DOM Events
React uses a ["Synthetic Event Wrapper"](https://facebook.github.io/react/docs/events.html) to create the internal React event system, which is not too different from native browser DOM events but with minor changes in syntax to indicate that this is an event that happens inside of a React component to be translated by React to the browser by way of the React `render` method. If you need access to the native browser event for a special reason you have access to it through the `nativeEvent` method, but you will mostly be using synthetic events provided by React that are normalized across browsers so that they behave consistently. 

Handling events is also similar to native DOM events, with syntax differences. React event names are camelCase rather than lowercase, and rather than passing a string reference to a function for execution you would pass a function expression as reference inside of curly brackets, `{someFuntion}` this is true anytime you pass React a function expression, for example passing a boolean value as a prop to a component would be `myBoolenaProp={true}` not `myBooleanValue="true"`, the latter would be interpreted a string and not evaluated as an expression that returns a boolean value. Native browser DOM `onclick` below

    <button onclick="activateLasers()"> 

becomes 

    <button onClick={activateLasers}>



## Conditional Rendering
Any good UI templating framework provides a way to apply conditional logic in order to determine how something is rendered. You can create variables that return a React Element, since we are using JSX we can simply return a valid JSX element, with a single root node, from a function expression with a variable name, and later use the variables as options for rendering output based on some conditional logic. Taking an example from the React docs let's use the example of a "Log In" and a "Log Out" button, the component state will determine which button to show the user. Switch to "conditional-render" branch now. First we declare our button functions.

    function LoginButton(props) {
      return (
        <button onClick={props.onClick}>
          Login <span><i className="em em-lock"></i></span>
        </button>
      );
    }

    function LogoutButton(props) {
      return (
        <button onClick={props.onClick}>
          Logout <span><i className="em em-unlock"></i></span>
        </button>
      );
    }

Next we create our React Component, use the constructor method to initialize our component with static methods for handling click events and defining an instance property for component state. 

    export default class Header extends Component {
      constructor(props) {
        super(props)
        this.handleLoginClick = this.handleLoginClick.bind(this)
        this.handleLogoutClick = this.handleLogoutClick.bind(this)
        this.state = {isLoggedIn: false}
      }

      handleLoginClick() {
        this.setState({isLoggedIn: true})
      }

      handleLogoutClick() {
        this.setState({isLoggedIn: false})
      }

      ...

Now inside our render function we perform some conditional logic to determine which button to show based on component state.

    render() {
      const isLoggedIn = this.state.isLoggedIn;

      let button = null;
      if (isLoggedIn) {
        button = <LogoutButton onClick={this.handleLogoutClick} />;
      } else {
        button = <LoginButton onClick={this.handleLoginClick} />;
      }

      ...

And finally we return our React Elements with the button varaible passed in a function expression inside of curly braces.

    return (
      <header>
        <div className={s.user}>
          {button}
        </div>
      </header>
    )


Switch to "logical-operator" now.




Switch to branch "forms-1" now.
<!-- TODO: Form content here -->


## Routing







### References
<!-- - [Containers and Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.tvxsigg7y) -->
<!-- - [Animated Gradient on Codepen](http://codepen.io/13twelve/pen/xLoiH) -->
<!-- - [ClassNames plugin](https://github.com/JedWatson/classnames) -->
- [Let's Talk About Pay](https://modelviewculture.com/news/lets-talk-about-pay)
- [Emoji CSS](https://afeld.github.io/emoji-css/)
- [Emoji Cheatsheet](http://www.webpagefx.com/tools/emoji-cheat-sheet/)
- [React Supported Events](https://facebook.github.io/react/docs/events.html#supported-events)
- [React DOM Reconciliation](https://facebook.github.io/react/docs/reconciliation.html)
- [Component Lifecycle](https://facebook.github.io/react/docs/component-specs.html)
- [JavaScript Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
