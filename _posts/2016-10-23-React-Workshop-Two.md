---
layout: post
title:  "React Workshop Two"
date:   2016-10-09
categories: meetup
author: avajessica
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


Switch to "logical-operator" now. If you wanted to hide or show some information based on state you can use the logical `&&` operator preceded by an expression that evaluates to a boolean, if the expression evaluates true it will render the React Element if not it isn't rendered to the page. 

      { this.props.messages.length > 0 && this.state.isLoggedIn &&
        <p>
          <span><i className="em em-mailbox_with_mail"></i></span>
          You have {this.props.messages.length} unread messages.
        </p>
      }

Switch to "conditional-operator" branch now. As you can see if you have only one message in your box the plural form of the word messages would look odd. To demonstrate another conditional render expression we can test for the length of the messages array and use the JavScript ternary operator to evaluate a boolean for which string to show, either "messages" or "message" based on the length of the messages array.

     {this.props.messages.length > 1 ? ' messages' : ' message'}.


## State and Forms

React components, like the Form we're going to create this week, are essentially "state machines". This is like a pattern for making clothes.  You have a pattern, it can accept different sizes and fabrics.  The pattern doesn't care what size or what fabric it gets. It will behave the same in any size or any fabric.  If you make the dress with a flowered courdoroy, or a black satin, the dress, (or the rendered element), will be the same layout and design. In the case of a React component, state is like the dress fabric.  This means that everything flows in one direction - from the parent or wrapper component to the child componenet.  The child accepts props from the parent, and keeps its own internal state, and renders according to the values of the props and state.

Each React component has a state property.  This state property reflects the UI state of the component.  When a user takes action on a form element, the component's state will update.  When the state is updated, using `this.setState()`, the component is re-rendered, this time applying its updated state.  So you can imagine a lot of rendering goes on - if you're updating state every time a text input changes, that's a re-render on every key stroke.

### Create a Form

Let's get started. First, checkout the `form` branch.

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

## Routing
Routing is a pretty straightforward addition to our application. We will use the React Router library, it has active contributors and has been keeping up with updates and user issues. Switch to "routing" branch now, and if you haven't already `npm install` to get the React Router modules. Looking at out entry point `src/index.js` you can see that have changed the way we mount our application, you might also notice that we have added a new directory called containers, we will be discussing the container/component in more detail in workshop three, but for now it is good enough to notice that the containers have names that indicate their function and can correspond to their route names, "Root", "Home" and "Signup". 

We are still calling to `ReactDOM` to render our application but we are now passing JSX for router components rather than our own components. The structure of the router JSX is hierarchical and represents the route tree for our application.


    ReactDOM.render((
     <Router history={browserHistory}>
      <Route path="/" component={Root}>
        <IndexRoute component={Home} />
        <Route path="signup" component={Signup} />
      </Route>
    </Router>
  ), document.getElementById('app'))


The top level component `Router` passes in `browserHistory` which we have imported from React Router. The history object is what listens to the browser location object, `window.location`, and parses the object for use by React Router and your application. The router expects children of the `Route` type, to which we pass two props, a path and a component. `Route` components can also have children passed to them and this will create a path hierarchy for you, for example if you were to have a mailbox route that has paths for incoming and outgoing messages that look like the following.

    /mail/incoming
    /mail/outgoing

You could setup your route components like so.

    <Router history={browserHistory}>
      <Route path="mail" component={Mailbox}>
        <Route path="incoming" component={Incoming} />
        <Route path="outgoing" component={Outgoing} />
      </Route>
    </Router>


We have use a component called the `IndexRoute` and that is a special component that provides a default child component to a top level route, in our example the top level route is the root of our application and our `IndexRoute` component is called "Home". You might be curious what does the Root container render if Home is our root path? The Root container is parent to the entire application and I like to use it to hold things that are site wide like headers and footers, and I generally like have the `App` component, which includes our base CSS styling, to be it's top level element. Both `Root` and `App` components pass child components through using `{this.props.children}` and function as wrappers.

    export default class Root extends Component {
      render() {
        return (
          <App>
            <Header messages={mail} />
            <main>
              {this.props.children}
            </main>
          </App>
        )
      }
    }

Looking into our Homepage container, `src/containers/Home.js` there is another React Router component we are using called `Link` and it does exactly what you think it does, the `to` attribute is synonymous with the anchor's `href` attribute it provides an internal link to a path defined in your Router component. You would only use the Link component when linking internal routes, if you are linking to a location outside of your application you would use the native `<a href="#">` tag.

    <Link to="signup">here</Link>

That should be enough to get you started hacking around with the Router and application setup, next time we will get into the details of the container component pattern that we have started, Redux, what it is, why use it and some principles of functional programming that have inspired React best practices.

### References

<!-- - [Animated Gradient on Codepen](http://codepen.io/13twelve/pen/xLoiH) -->
- [Let's Talk About Pay](https://modelviewculture.com/news/lets-talk-about-pay)
- [Emoji CSS](https://afeld.github.io/emoji-css/)
- [Emoji Cheatsheet](http://www.webpagefx.com/tools/emoji-cheat-sheet/)
- [ClassNames plugin](https://github.com/JedWatson/classnames)
- [React Supported Events](https://facebook.github.io/react/docs/events.html#supported-events)
- [React DOM Reconciliation](https://facebook.github.io/react/docs/reconciliation.html)
- [Component Lifecycle](https://facebook.github.io/react/docs/component-specs.html)
- [ES6 Class Syntax in React](https://facebook.github.io/react/docs/reusable-components.html)
- [ES6 Classes in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/
Classes)
- [React Router](https://github.com/ReactTraining/react-router)
<!-- - [Containers and Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.tvxsigg7y) -->
- [React Router History Guide](https://github.com/ReactTraining/react-router/blob/master/docs/guides/Histories.md)
- [History API](https://developer.mozilla.org/en-US/docs/Web/API/History)


