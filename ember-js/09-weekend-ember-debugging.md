As we develop in Ember, we'll find that keeping the JavaScript Console open will help us understand what is happening as our code is processed.  In this lesson, we'll explore some  tools for the console that help us follow and troubleshoot our code.

## Ember Inspector

The Ember Inspector tool can be added as an additional tab on the JavaScript console by installing it on your machine.  It is available for many different browsers. For Chrome, click [Ember Inspector for Chrome](https://chrome.google.com/webstore/detail/ember-inspector/bmdblncegkenkacieihfhpjfppoconhi).

**Recommended screencast**: Robin Ward's [How to debug an Ember.js application](https://www.youtube.com/watch?v=mXHzC0LdTuk) shows Ember Inspector in action.

## Error Messaging - Server Logs and Console

If something did not work as expected, the first two places to check for messages are the server logs and the console.  

When we start the Ember server, JSHint runs to catch any possible errors or oversights in our JavaScript code.  It will catch variables that are not defined, or defined but not used.  It will note where semicolons are missing.  Often correcting any issues JSHint identifies will correct behavior that we may see in our applications.  

Running the application with the console open will allow us to see any potential errors that are generated while our code is at work.  

## Debugger and JavaScript Alert/Console.log

Don't forget to use the JavaScript `debugger` and/or JavaScript alerts or console logging to pinpoint issues that arise in your code.  Ember includes [helpers](http://guides.emberjs.com/v2.0.0/templates/development-helpers/) for logging and using debugger in Handlebars templates, too with `{{debugger}}` and {{log 'In route now!  'City is ' city.name}}. 

## Warnings

Warnings are notifications of something to be considered in your code.  They are not errors that prevent the application from running but should be understood before being disregarded.  

### Deprecations 

Deprecation warnings notify developers that the code that they are implementing still works but is no longer the preferred usage.  Usually the reason code is deprecated is that it has been replaced by an improved alternative.  Rather than remove the "old" way, it is deprecated to allow for backward compatibility and give developers time to update their programs.
