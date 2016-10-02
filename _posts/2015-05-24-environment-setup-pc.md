---
layout: post
title:  "Environment Setup for PC"
date:   2015-05-24
categories: meetup
author: ava
---

## Introduction
Welcome to the first meetup topic Environment Setup. The following post will outline the steps for installation of the tools needed to run our [Environment Code](https://github.com/SeattleLadiesJS/Environment-Setup){:target="_blank"}. Ideally you will have ran through all the steps and have all the tools installed before the meetup. If you run into problems getting these items installed I encourage joining our [slack](https://seattleladiesjs.slack.com/){:target="_blank"} group and posting your questions to the "#environment-setup" channel. If you need an invite to our slack group please send your name and email address to [seattleladiesjs@gmail.com](mailto:seattleladiesjs@gmail.com). 

## Operating System

First best thing to have is an up to date version of your OS, this post is using Windows 7, go to Start menu and run Windows Update. Another good thing to know before getting started is if you are running a 64-bit version of Windows so that you can choose the correct `.exe` installers to use, you can read more about what that means and how to find out which version you have [here](http://windows.microsoft.com/en-us/windows/32-bit-and-64-bit-windows#1TC=windows-7){:target="_blank"}. 

## Code Editor

Next thing we need is a code editor, it can be any code editor that you prefer but I will be using SublimeText3 and would recommend it as a nice simple to use code editor, [download here](http://www.sublimetext.com/3){:target="_blank"}.

Our environment setup is using an [EditorConfig](http://editorconfig.org/){:target="_blank"} which helps maintain consistency of coding styles across different types of editors and IDEs, if you are using an editor other than SublimeText3 there should be a plugin available, you would have to research your editor to find plugin install instructions. 

For installing EditorConfig with SublimeText3 you have to use the Package Manager which is a plugin for SublimeText3 that needs to be installed separately, [instructions on how to install here](https://packagecontrol.io/installation){:target="_blank"}. Once you have Package Manager installed you can add packages to customize SublimeText3 to suite your development style. 

Package Control is driven by the Command Palette, to open the palette, press `ctrl+shift+p`.  A drop down should appear start typing "Package Control: Install Package" until the menu option appears, select it and hit enter. A new drop down will appear then type in the name of the package you would like to install, in this case type 'EditorConfig', select it and hit enter. You can see the package install progress in the bottom left corner of SublimeText. When it has finished you must restart SublimeText for it to be activated.

There are lots of great packages for SublimeText that you can install to customize your environment, none of them are required but there are a few that I use and recommend below.


- [Emmet](http://emmet.io/){:target="_blank"} - HTML snippets.
- [SublimeLinter](http://sublimelinter.readthedocs.org/en/latest/index.html){:target="_blank"} A plugin for Sublime which allows you to add packages for linting your code.
  - [SublimeLinter-html-tidy](https://github.com/SublimeLinter/SublimeLinter-html-tidy){:target="_blank"}
  - [SublimeLinter-csslint](https://github.com/SublimeLinter/SublimeLinter-csslint){:target="_blank"}
  - [SublimeLinter-jshint]( https://github.com/SublimeLinter/SublimeLinter-jshint){:target="_blank"}
- [JavaScript Snippets](https://github.com/jprichardson/sublime-js-snippets){:target="_blank"} - Snippets for JavaScript.
- [Local History](https://github.com/vishr/local-history){:target="_blank"} - Maintains a local history of files.
- [Sass](https://github.com/nathos/sass-textmate-bundle){:target="_blank"} - Snippets and highlighting for sass.
- [DocBlockr](https://github.com/Warin/Sublime/tree/master/DocBlockr){:target="_blank"} - Assists in writing comment blocks.
- [AlignTab](https://github.com/randy3k/AlignTab){:target="_blank"} - Assists in code alignment.

## Git

Before we are ready to start working with code we need to have a system in place to manage that code, we'll be using Git. If you have never heard of Git before I would suggest reading [GitHub For Beginners: Don't Get Scared, Get Started](http://readwrite.com/2013/09/30/understanding-github-a-journey-for-beginners-part-1){:target="_blank"}. Another great read for all levels of experience is [Getting Started Git Basics](https://git-scm.com/book/en/v2/Getting-Started-Git-Basics){:target="_blank"}.  

Git is the most popular code repository and version control system in open source and it can have some very technical aspects to it, but for now let's focus on getting it installed. First thing to do is [download a copy of msysgit](http://msysgit.github.io/){:target="_blank"} for Windows and run the installer using the default options. After installation you should have a new Program folder called Git, we will be using the Git Bash application which provides a [BASH](http://en.wikipedia.org/wiki/Bash_%28Unix_shell%29){:target="_blank"} shell emulation to run Git from the command line using a syntax that is used in UNIX environments. There is also a GUI application that will be installed, we won't be using it.

Now that you have Git installed let's go git some code. 

Open the Git Bash application. In the command line type `pwd` and hit enter to "print working directory" and tell you where your command line is operating from inside your system files. Hopefully you are in your User home directory, `c/Users/<username>` if not you can change directory by typing `cd "${HOME}"` and hit enter. 


Type `git clone https://github.com/SeattleLadiesJS/Environment-Setup.git` and hit enter. If everything is setup correctly you should now see a new folder created in your User home called "Environment-Setup" which includes the code files that we will be using for our Environment Setup. 

*Note For meetup we will be forking this repository into your own git repository which means that you'll need to have a github account, be sure to have signed up for your own account at [github.com](https://github.com/){:target="_blank"} before the meetup if you don't already have an one.


## Node.js

Last step to getting our Environment running is to install Node.js, which is a runtime environment for JavaScript. If you're not familiar with Node.js I would suggest watching [What is Node.js Exactly? - a beginners introduction to Nodejs](https://www.youtube.com/watch?v=pU9Q6oiQNd0){:target="_blank"}.

First thing to do is [download a copy of Node.js](https://nodejs.org/download/){:target="_blank"} and run the installer. When it has finished you will need to restart your computer to run Node.js. 

Let's test to make sure we have everything installed correctly. In Git Bash type `node -v` hit enter, you should see v0.12.4 the current version of Node.js. Now let's test that you have Node's package manager installed as well by typing `npm -v` and hit enter you should see 2.10.1.

Before we an run our Environment Setup project we need to get a couple of frontend tools installed, [gulp](http://gulpjs.com/){:target="_blank"} and [bower](http://bower.io/){:target="_blank"}. 

  Type `npm install -g gulp bower` in the Terminal and hit enter. 

Almost ready to run our project, we still have a few more things to install.

  Type `cd "${HOME}/Environment-Setup"` and hit enter

Now we can install all the required modules we'll need from NPM and Git:

Type `npm install` and hit enter 

Type `bower install` and hit enter. 

Lastly type `gulp` and hit enter, you should see output which is similar to a help menu for command line which lists the config file for the project followed by Main Tasks and Sub Tasks.



## Conclusion

I know we just blew through a lot of important stuff without much explanation but hopefully everything installed smoothly and we can spend our time in the meetup answering questions and learning about the code that is included with the sample repository. 
 

