---
layout: post
title:  "Scope and Closure"
date:   2015-09-17
categories: meetup
author: ava
---

The topic for discussion is scope in JavaScript, what it is, why it is needed and how it is defined and used to create "closures" in our functions. Much of the source material for this discussion has been gleaned from Kyle Simpson's book ["You Don't Know Scopes & Closures"](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md#you-dont-know-js-scope--closures) and is free to read online at github.

#What is Scope?
Scope centers around the fundamental ability to define and store values in a variable, and how we access those values afterwards. Essentially scope is a set of rules defining where a variable lives in the code. An over simplified way to describe scope would be to say that it describes the execution context of the inside of a function and thus "Global Scope" is the execution context of the JavaScript engine. In the following statement the global scope holds the variable `a` and the scope of the function HelloWorld holds the variable `b`.
    
    var a = "Hello";

    function HelloWorld(){
      var b = "World";
      return a + b;
    }


While that seems straight forward there is more to understanding scope that this and in order to really get scope we need to look at how JavaScript code actually gets executed.

## Compilation
It is often mis-stated that JavaScript is not a compiled language, it is most definitely compiled, as all languages are, it's just not compiled at the time of authorship, it is compiled at the time of execution, making it a little bit different from other traditional languages in that we are not made aware of errors until the code actually runs. The compiler is called the ["JavaScript engine"](https://en.wikipedia.org/wiki/JavaScript_engine), which is a virtual machine that interprets and executes the code at runtime using "JIT", or just in time, compilation techniques. A few of the most well known engines are the [V8](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) which powers node.js, [Gecko](https://en.wikipedia.org/wiki/Gecko_(software)) an open source engine used in many browsers, and [SpiderMonkey](https://en.wikipedia.org/wiki/SpiderMonkey_(software)) which was the very first engine created by Brendan Eich at Netscape. 

In traditional compilation code will go through a set of steps before being executed, at a very high level the steps are as follows.

  - **Tokenizing**, or sometimes called **Lexing**, where the input stream of code is broken up into meaningful tokens that can be passed onto the next step.
  - **Parsing**, the parser has an understanding of the language's grammar and is responsible for identifying errors and composing an internal grammatical structure of the code into a "parse tree", or as Kyle refers to it an "AST, abstract syntax tree".
  - Creating **machine code** or **bytecode**, depending on the engine, which translates to numeric codes and addresses for an interpretor to execute.

## Optimization and Strict Mode
  Somewhere between parsing and creating bytecode is where the compiler magic happens, this is where the compiler optimizes code for the best performance on the engine. This is also where JavaScript engines differ greatly from other compilers in that they don't have alot of time to make these optimizations. This is why the use of best practices in code like "use strict" and not using eval statements really does improve performance at runtime. *[Strict Mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode) restricts the usage of any code that cannot be optimized for by the engine making the code run faster than code that has not been optimized in this way.*

## Lookups
So back to scope, earlier it was stated that scope is a set of rules defining where a variable lives. When you think about the statement `var a = 2;` what comes to mind about how variable `a` is created and how can the value of `2` be stored and later accessed. Try in plain English to come up with the pseudocode to make this happen, something like assign memory to an address that is named `a` and assign the value of `2` to it. That sounds good but what happens when another memory location is also called `a`? This is where scope comes in with it's rules of looking up memory for identifiers and looking up identifiers for values. There are two types of lookups, LHS and RHS. LHS stands for "Left Hand Side" and RHS "Right Hand Side". The left/right hand sides refer to what we are looking up in the assignment operation, but it doesn't always refer to a literal left and right. The left hand side lookup is trying to find an identifier so that a value can be assigned to that memory address, a right hand side lookup is trying to find the value stored in an existing identifier. The terms Left hand side and Right hand side can be confusing because they don't technically map to the left and right of an assignment operator, RHS lookups refer to anytime a value is being retrieved, such as in the statement `console.log(a)`, here `a` is a RHS lookup and there is no assignment operator to be on the right of, to simplify this think of Left as the target and Right as the source.

## Nested Scope
Scope lookups always start at the innermost block of code being executed at the time, if it can't find what it is looking for in the current block it will move upwards and into the parent block until it reaches the top level or global scope. If the same identifier is present at different levels this is called "shadowing". If the look up reaches the top level and cannot find what is looking for it will react differently depending on what type of lookup it is performing. If a LHS reaches the top level and cannot find a match, it will create the variable as if it were a new declaration, if a RHS lookup reaches the top level without finding a match it will throw a `ReferenceError` which means that the variable you are looking for is `undefined` and does not exist.

## Exercise
  The following material is taken directly from Kyle Simpson's ["Engine/Scope Converstaion"](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/ch1.md#enginescope-conversation) in the book "You Don't Know Scopes & Closures". 

  The exercise is one of having a conversation as if you were a compiler processing some JavaScript. The actors in this conversation are the 

  - **Engine**: responsible for start to finish compilation and execution of the JavaScript program
  - **Compiler**: handles all the dirty work of parsing and code-generation 
  - **Scope**:  collects and maintains a lookup list of all the declared identifiers (variables), and enforces a strict set of rules as to how these are accessible to currently executing code

  Let's talk through the following code


    function foo(a) {
        console.log( a ); // 2
    }

    foo( 2 );

and for good measure let's test ourselves with the Quiz! *(if you're reading this alone you can follow the conversation at the link above)*

    function foo(a) {
        var b = a;
        return a + b;
    }

    var c = foo( 2 );


#What is Closure
"What is a closure in JavaScript?" This question seems to come up alot in interviews, most likely because the answer can give insight into a candidates understanding of how JavaScript works and how code can be patterned to create modular components of code inside a large application. The most basic answer would be that it is a function which encloses another function that has access to variables inside it's own scope as well as it's container's scope and the global scope but that isn't the whole answer and you should expect a follow up question of "can you explain why this is important or give an example of how this can be used in an application?" 


## Patterns
There is a concept in software design called [The Principle of Least Privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege), and what it boils down to is the practice of hiding the parts of code that are not essential and only exposing the things that can access the functionality required to be useful. This is what an API is, an *Application Programming Interface* that exposes only the useful parts of the program to the outside. This is also the basis and purpose of a closure. Hiding non essential variables and functions from the outside world are an important pattern that is required to build large robust applications. 


## Functions
In the example of an application that is made up smaller script files, let's call them modules, you do not want to pollute the global scope with all the variable and function names that were used to create your module. A common practice is to create closure around all of the module code by wrapping it in an immediately invoked function expression, or **IIFE** for short, and return only an API. So what is an IIFE anyway? The answer comes by asking a few more questions about functions, what is an anonymous function, what is a function expression and what makes it self executing. 

An anonymous function is a function that is not bound to an identifier, which dictates that anonymous functions can only be implemented using closure, in other words they can only be executed if they are inside the scope of another function that is bound to an identifier. The most common usage of anonymous functions are when functions are passed as parameters to other function as "callbacks". 

    setTimeout(function(){
      console.log("I'm executing inside an anonymous function!")
    }, 1000);

An anonymous function isn't required to create an IIFE, or even a callback, but it is the most commonly used pattern. The following is also a valid callback and probably better code style because you have an identifier bound to the results, which can be very helpful when debugging or reading a stack trace.

    setTimeout(function timeoutCallback(){
      console.log("I'm executing inside a function expression!")
    }, 1000);

A function expression is any function that does not begin with the keyword "function". Most common use is something like  `var myFunction = function(){};` but even adding a semi-colon before a function definition `;function myFunction(){}` qualifies as a function expression.


And lastly a function expression that executes immediately is a function that is wrapped in `()` and is followed by `()`, the trailing pair of `()` is what does the immediate execution. There are a couple of ways to write this, both are valid and the difference is purely stylistic. `(function(){})()` and  `(function(){}())` are the same thing technically. If you put something inside the trailing `()` it will be passed into the function expression as arguments. So in our example of creating a module that returns an API we could practice using closure something like this.

    (function myModule(window){
      
      var myApi = {
        doSomething: function (){ return something;},
        doAnotherThing: function(){ return somethingElse; }
      }

      window.myApi = myApi;

    })(window);




<!-- ## A Much More Technical Answer
Now we know why the closure pattern is important to create modules, and we know the syntax for doing so, but we still haven't fully explained what a closure is technically. The Nitty Grtitty, as explained in Kyle's book, is "Closure is when a function is able to remember and access it's lexical scope even when that function is executing outside its lexical scope." So here comes some of the compiler logic that we practiced earlier, and the term "lexical scope", if we look back at the first step, the Tokenizing  -->

## Exercise
Now that we are able to answer the questions what is a closure, why closure is important and we can give an example of how to use it let's do an exercise to solidify our knowledge.

What would the following output be?

    for(var i =1; i <=5; i++) {
      (function(){
        setTimeout(function timer(){
          console.log(i);  
        }, i * 1000);
      })();
    }

How can we use closure to output something different?





