---
layout: post
title:  "React Workshop Three"
date:   2016-11-06
categories: meetup
author: ava
---

## State

As we have seen React components are views that encapsulate their own state. State is used internally to respond to browser events, like entering text into a form or clicking a button, and updating the view. We know that there are events that can update the views during the React Lifecycle which can change and cause an update in the view. We've also seen how to change views by adding in Routing, but what happens to component state when we change routes? 

Switch to `state` branch now. To better explain application state I have moved our `Header` component out of the `App` component which is shared from the `Root` container, and instead placed a copy of the `Header` component on both the `Home` container and the `Signup` container. This is redundant and not something you would probably want to do, but it demonstrates what application state is. If you start up the app you will see that there is a login button on the homepage and the signup page, but if you click login on the homepage and change routes to the signup page the state of the login component has changed back to the default state declared internally in the `Header` component.

    const isLoggedIn = this.state.isLoggedIn;

    let button = null;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }



So how we can share the state of being logged in across components if we need to know the status of a login or logout event in different views or different components? 

Well we know that props can be passed into components to update their view state, so what if instead of the `Header` keeping track internally of logged in state we pass in the logged in state from a prop. 

     <Header messages={mail} loggdIn={true} />

We still have to figure out a way to share that state between the `Home` and `Signup` containers. That is what Application State is and that is where Redux comes in, technically we will be using *React Redux*, [Redux](http://Redux.js.org/) itself is framework agnostic.

## Redux

Redux comes from a purist place, a place of clean functions that have a single responsibility and a predictable outcome each time they are executed. This place is Functional Reactive Programming, and it is a workshop unto it's own that we should definitely do someday, but for now the important things to remember are:
  
  - Single responsibility functions.
  - The product of the function will always return the same value given the same input is passed in.
  - Asynchronous data flows propagate change in a one way direction.


 In this way Redux avoids mutating state and instead creates a "pure" function that can be called with a set of parameters defining application state and returns the same state each time the same parameters are passed in. 

    function updateState(state, changeInState) {
      return Object.assign(state, changeInState)
    }

    updateState({loggedIn: false}, {loggedIn: true})

Mutating state is what happens when many functions are acting upon a single state object that they share. For instance if we tried sharing our logged in state with a global variable on the `window` object we could share that object across React views by referring to the window object for our logged in state.

    window.state = {
      loggedIn: true
    }

Mutating is what would happen if another component wants to update the same `window.state` object with something new, let's say the mailbox now wants to keep the number of messages in the `window.state` as well. It would do something to add the mailbox property to the state.

    window.state.messages = 3

This makes `window.state` vulnerable to mistakes, there is nothing preventing the state object from being changed in something else entirely. A function that takes in the current state and the updated state and returns a brand new state with the new properties merged could help maintain the state object. 

    
    // value of current state object
    state : {
      loggedIn: true
    }

    //mailbox wants to add messages to state
    updateState(state, {messages: 3})

    // results in 
    state: {
      loggedIn: true,
      messages: 3
    }

This is the first principle of Redux, which is that everything that changes in your application, including data and the view state, is contained in a single object called the state or the state tree.



## Actions

The second principle of Redux is that the state tree is not mutable, which we have also seen. But instead of our `updateState` function. Redux has a concept called *Actions* which you can *dispatch* to update the state. Actions describe the changes to be made. Switch to `redux` branch, do an `npm install` and open up `src/redux/index.js`

If we want to describe the state change that happens after logging in we would create an action for that. Actions are plain JavaScript objects that have one requirement, that they have a `type` property defined which indicates the type of action being performed. In our case logging in would only be changing the `loggedIn` property so our action could look like

    {
      type: 'LOG_IN'
    }

It is common to see a file with many `const` string values at the top which define the action type rather than passing inline as above. This helps when an application size grows, it is easy to look at the defined action types in one place and get an idea of what the application does without reading too much into the underlying code.


## Action Creators

Action creators are functions that create actions, it's confusing terminology and easy to get mixed up, but know that the action creator is a function that creates an action.

    function logIn(loggedIn) {
      return {
        type: 'LOG_IN',
        loggedIn
      }
    }


## Reducers

What do we mean by "create" an action if actions are just JSON objects that we return from an action creator? Remember when I said that actions get "dispatched"? In JavaScript event management is most commonly managed via a dispatch/listen pattern, think about how we `addEventListener('click')` in the DOM, the listener is waiting for the browser to dispatch a click `event`, and then it will do something in response to that `event`. It's pretty much the same thing, but instead of an event listener to handle the actions being dispatched we create "Reducers" to handle the state changes. 

It's called a reducer because it is the type of function you would pass to the `Array.prototype.reduce` method, wherein a function is applied against an iterated array of values and saved into a single accumulated value and finally returns that single value after all items have been passed through the reduce function. 

You begin a reducer by defining the initial state, this is a chance to set default values for application startup. The reducer function uses a switch statement to only update the data in the app that is described by the action type. 

     switch(action.type) {
      case LOG_IN:
        return {...state, loggedIn: action.loggedIn}
      default:
        return state
    }

You can see this reducer will update the state when a `LOG_IN` action type is passed into it. Another aspect of a reducer is that a default case which returns the `state` is required, and that is for the first run when the application is bootstrapping itself with the initialState. 

## Store

The Store is what brings together the event dispatching and state updating by providing a minimal API. The Redux store has the following responsibilities.

- Hold application state
- Allows access to state via `getState()`
- Allows state to be updated via `dispatch(action)`
- Registers event listeners via `subscribe(listener)`
- Handles unregistering of listeners via the function returned by `subscibe(listener)`

The store is provided by the Redux library, looking at `src/index.js` our application entry point, you can see that we have imported our Redux reducer into a variable named `app` and we have imported the `createStore` method from Redux, we create the store by passing in our reducer function. For now we are console logging the current state bu using the `getState()` method.

## Using Redux with React

Up to this point everything about our usage of Redux has been agnostic of the React framework, you can use Redux in any JavaScript application but you would have to manage how to share that state across your components. We could simply pass `state` into our components and they would all have access to it but we would have to manually subscribe to the updates using `store.subscribe()` which is not ideal, it is also not advised since there is a library that will manage the bindings to React components for you called React Redux. Switch to `react-redux` branch now.

Now you can see we have added an import to `react-redux` to get a `Provider` component. The Provider does what it sounds like it does, it provides the store the child components that are passed into it, since we are passing in our Routes we are passing in all the React components in our application. So does that mean that we can access `store` as a prop to our components now? Technically you could, but you have to use the `connect()` method provided also from React Redux library in order to get connected to the store.

If you look at either the `Home` container now you can see that we are importing `connect` from `react-redux` and we are no longer exporting the `Home` class as the default export component, it just a declared class now.

    class Home extends Component {
      render() {
        return (
          <div>
            <Header messages={mail} />
            <h1>Home</h1>
            <p>This is the homepage, if you'd like to see our form click <Link to="signup">here</Link></p>
          </div>
        )
      }
    }

Instead we are exporting as our default a call to the `connect()` method, to which we are passing in two helper functions which will manage our subscriptions and dispatching of the logIn action creator. The first function expression `mapStateToProps` is mapping the application `state` to the `Home` component's incoming props, so now you can access the logggedIn status inside of `Home` using `this.props.loggedIn`.

    const mapStateToProps = (state) => {
      return {
        loggedIn: state.loggedIn
      }
    }
    
The second function expression `mapDispatchToProps` manages the dispatching of actions for us.

    const mapDispatchToProps = (dispatch) => {
      return {
        onLogIn: () => {
          dispatch(logIn())
        }
      }
    }

And finally the `connect` method we pass in our mapping expressions, and because `connect` is called as an [Immediately Invoked Function Expression (IIFE)](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression) we pass our `Home` container into scope so that it can be connected and returned as our default export.

    export default connect(mapStateToProps, mapDispatchToProps)(Home)


We're almost ready to dispatch our log in action and use our shared state, but we need to modify the Header component to accept a prop for `isLoggedIn` rather than have it be something that the internal component state manages. Switch to the `dispatch` branch now. In the `Header` component we are changing 

    const isLoggedIn = this.state.isLoggedIn

to 

    const isLoggedIn = this.props.isLoggedIn

We also need to change the methods which update state from internal methods to passed in props from it's parent container, `Home` in this case, which is connected to the state.  This means we can remove most of the logic code inside the `Header` and just return a React DOM object that is getting it's state managed by Redux. This changes our `LoginButton` from 

     button = <LoginButton onClick={this.handleLoginClick} />

to this

     button = <LoginButton onClick={this.props.handleLoginClick} />

And you can see now when added the `Header` component to our page we are passing props for handling the LogIn event. 

    <Header messages={mail}
      isLoggedIn={this.props.loggedIn}
      handleLoginClick={this.props.onLogIn.bind(this)}
    />

If you log in on the homepage and navigate to the Signup page you can see that you stay logged in. 

## Container Components

Now that we have a very simple example working for Redux we can talk about the container/component organization we have started in our code. This is a design pattern to organize components into distinct sections that each address a separate concern, [Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns). The containers are the connected to the application state, but the components are not. The components have styles associated with them, but the containers do not. This should indicate that the containers are running the application and the components are presenting the UI. By keeping presentational components "dumb" they are controlled by the containers through props, this  makes the components portable they can be moved into another application all together as long as the props are passed in.

## Your Turn

I know that was a lot to grok but like most things if you practice using the patterns, even blindly in the beginning, eventually you will understand the whole. That is my best advise for using React, and the whole ecosystem, don't feel you have to fully understand *everything* we covered, and things beyond, to start using it. I hope that these workshops have given you a high level explanation of what is involved with development environment and a set of patterns that you can hack around with. 

Now your turn, implement the log out function using the patterns from log in. If you get stuck you can checkout the `logout` branch for an example of how it might be completed.


### References


- [Functional Reactive Programming](https://en.wikipedia.org/wiki/Reactive_programming)
- [Object.assign()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
- [Managing events with dispatch and listen](http://krasimirtsonev.com/blog/article/javascript-managing-events-dispatch-listen)
- [Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
- [Redux Videos](https://egghead.io/series/getting-started-with-Redux)
- [Redux with React](http://Redux.js.org/docs/basics/UsageWithReact.html)
- [React Redux API](https://github.com/reactjs/react-redux/blob/master/docs/api.md)
- [Presentational and Container Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.jw5mq66ox)
- [React Community](https://github.com/reactjs)

