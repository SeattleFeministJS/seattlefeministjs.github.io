---
layout: post
title:  "Building Mobile Apps with Ionic"
date:   2016-04-21
categories: meetup
author: pattyok
---

## About Me
Patty O'Hara  
Front-End Web Developer  
2014 - 2015 Worked on [MyNeighbor App](https://myneighbor.com/)  
Built with:

 - Ionic: AngularJS, HTML5, SASS
 - Node, MongoDB back-end

## What is it?
 - [Ionic](http://ionicframework.com/) is a framework that includes angularJS, cordova, & HTML5 & CSS components
 - It allows you to build mobile apps with one codebase that works on iOS, Android, and Windows (aka Hybrid Apps)
 - Includes built-in angular *directives* and *services* that allow you to easily include common mobile patterns in your app
 - More than a framework, also available are all of the tools you need to build, test and deploy your app.
 - Currently in version 1.2, version 2 is in Beta that will follow the official release of Angular 2

## "Are Hybrid Apps Really a Thing?"
 - Yes! You can build fully-functioning apps that resemble native apps with javascript
 - The Ionic Team continually works on the suite of tools to make development easier and allow you to build apps that function just as well as a native app.
    - Well documented
    - Ionic forum is well-managed, monitored for bugs and ideas for improvement
    - With MyNeighbor, Our first iterations were built with Ionic pre version 1. We saw improvements in the set of tools as we were working.
 - We saw some loss of "slickness" in the app vs a native app, but saw improvements as updates to Ionic were released
 - iOS is definitely easier/more consistent than android (mostly due to inconsistencies in hardware)
 - Definitely a great tool for producing apps cheaply and quick, especially a no-brainer for:
     + Internal Tools
     + App v1

## Demo

### [Get Started](http://ionicframework.com/getting-started/)

 1. Install Ionic
     - [Requires node](https://nodejs.org/en/download/)
     - Install latest npm
        `$ sudo npm install npm -g`
     - install ionic and cordova
        `$ npm install -g ionic cordova`

 2. Start a project
    `$ ionic start myApp tabs`

 3. Run the project  
    `$ cd myApp`  
    `$ ionic serve` - preview in browser  
    `$ ionic serve --lab` -preview android/ios side-by-side (not an emulator, but can view differences in styling)

### Preview on Device or Emulator - IOS
`$ cordova platform add ios` - just have to to do this once  

#### View on device - requires an xcode 7+ and iOS 9
1. Connect your iphone to computer with USB
2. In your project folder find platforms/ios/myApp.xcodeproj - click on it to open in xcode
3. In xcode, in the top bar, next to the app name, click the dropdown to find your device
4. Click the 'Play' button
5. An error will pop-up, click 'Fix This' - to authenticate your phone to use your app - [more details](http://stackoverflow.com/a/33928777/3681880)

`$ ionic run ios --device --livereload` - to preview on phone  
`$ ionic emulate ios` - opens the emulator (requires ios-sim & xcode)

#### Debug iOS
Safari > Preferences > Advanced - check show Develop Menu, when plugged in, your phone should show up

### Preview on Device or Emulator - Android
`$ cordova platform add android` - just have to to do this once  

`$ ionic run android` - if your phone is connected, you can simply preview it there

#### Debug Android
Inspect the app by opening Chrome on your computer and entering `chrome://inspect` in the address bar  
`$ ionic emulate android` - opens the emulator (requires Android Eclipse)

## Related Tools
 - [Ionic icons](http://ionicons.com/)
 - [Ionic CLI](http://ionicframework.com/docs/cli/)
    - SASS - `$ ionic setup sass`
    - [App Icon and Splash Screen Generation](http://ionicframework.com/docs/cli/icon-splashscreen.html)
 - [ngCordova](http://ngcordova.com/)
     - [Install Instructions](http://ngcordova.com/docs/install/)
     - [Cordova Plugin Documentation](http://cordova.apache.org/plugins/)
 - [Ionic Platform](http://ionic.io/platform) - Beta
    - Develop, build, test, deploy
    - User Authentication - social integration, user management
    - Builds for all devices in the cloud (no mac required)
    - Allow testers to preview your app
    - Deploy to the App store, push updates immediately
    - Manage push notifications

    This is still in Beta but is exciting - it solves many of the pain points we experienced in developing our app  

     - Built/Deployed, managed plugins with **Steroids**
     - Deployed to testers with **HockeyAPP**
     - Push notifications via **PushWoosh**
     - Built our own **Express Apps** for managing users and push notifications
     - Wrote our own login/logout and **oAuth integration**

 - [Ionic View](http://view.ionic.io/)
     - Share your app with others in progress (before it goes to the app store)
 - [Ionic Creator](http://ionic.io/products/creator)
     - Rapid prototyping tool
 - [Ionic Lab](http://lab.ionic.io/)
     - If you prefer GUI over CLI

## Good Things to Know
 - The Ionic CLI tools are relatively new, and have some kinks. Testing on a device can be flaky. Most of the time you can get away with livereload/web browser view. But when you need to work on a feature involving the phone features like camera, this can be real frustrating.  
 If its a real problem, one alternative is [AppGyver Steroids](http://www.appgyver.io/steroids) - to handle the building/previewing/deploying. This has its own separate quirks, but pretty solid for dev/test workflow
 - Take the time to understand what is available to you with with the framework. If it feels like you are fighting the framework, you may want to rethink your strategy. Build with the basics first and then embellish as the design requires
     + What is the Ionic Way?
     + What is the Angular Way?
 - Think about the organization of your files - the MVC model of js/html  is not the most convenient when your app starts getting large
     + [https://scotch.io/tutorials/angularjs-best-practices-directory-structure](https://scotch.io/tutorials/angularjs-best-practices-directory-structure)
     + [https://www.airpair.com/angularjs/posts/top-10-mistakes-angularjs-developers-make](https://www.airpair.com/angularjs/posts/top-10-mistakes-angularjs-developers-make)
     +  But also BE WARNED only do this if you have a pretty solid understanding of angular structure and syntax, little mistakes can screw up your whole app, and can be hard to track down.
 - Ionic is a work in progress, you may run into some bumps - seek help in the forums/github issues, trust the Ionic Forum over Stackoverflow

## Learn More

[Example Chat App](https://github.com/pattyok/chat-app)

### Features
 - Connecting with Api's
 - Using the Camera
 - Somewhat re-organized files
