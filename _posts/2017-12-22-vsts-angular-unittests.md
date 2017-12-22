---
layout: post
title:  "Running Angular unit tests in VSTS - PhantomJs is dead to me"
date:   2017-12-22 10:22:52 +1100
comments: true
categories: VSTS, Angular, Unit Tests
---


## Background
A current project I am working on is a hybrid Angular 4 and Asp.NET core 2.0 web application. 
CI has been set up on Visual Studio Team Services (VSTS) and as part of the build we wanted to run the Angular unit tests (logical really). 

Did a bit of research and the recommended approach, in summary, is to install as dev dependencies phantomjs-prebuilt and karma-phantomjs-launcher, configure the karma config to include phantomjs as a browser. Then on VSTS add a new  command line task to run angular-cli (ng) with the following arguments: 
```
    test --watch=false --single-run=true --reporters=junit,progress --browsers=PhantomJs
```
Cool, do an initial build and the test run. But then a couple of days later this happened:
```
   SyntaxError: Unexpected token 'const'
  at http://localhost:9876/_karma_webpack_/vendor.bundle.js:1318
```
What the f***!. 

After much swearing, comparing commits and experiementation found the problem, PhantomJS only provides Javascript support upto ES5 beyond that it throughs a wobbly.

## The solution
The simple solution is to abandon PhantomJS and use Headless Chrome. 
Here is how it's done.

 install [GoogleChrome Puppeteer](https://github.com/GoogleChrome/puppeteer).
```
  yarn add puppeteer
  # npm i puppeteer -D
```
Modify Karma.config.js. add the following line above the module.exports statement
```JavaScript
    process.env.CHROME_BIN = require('puppeteer').executablePath();
```
Update the browser to include ChromeHeadless
```JavaScript
    browsers: ['Chrome', 'ChromeHeadless']
```
To test it locally run the following command.
```
   ng test --watch=false --single-run=true --reporters=junit,progress --browsers=ChromeHeadless
```
At this point it should all work.

Now on VSTS, in the build definition (and this assumes you already have a command to run npm install) add a new command line task.

![sample ng test task]({{"/assets/ng-test-task.png" | absolute_url}})

Done! angular unit tests are now running successfully in VSTS.

The final piece is to publish the test results.

Back in the karma.config.js modify or add the jUnitReporter configuration as follows:
```JavaScript
 junitReporter:{
      outputDir: '',
      outputFile: 'test-client.xml'
    }
```
Then the VSTS build definition add a new Publish Test Results task and complete it has in the screenshot below.
[Junit publish test results]({{"/assets/publish-ng-test-results.png"| absolute_url}})



