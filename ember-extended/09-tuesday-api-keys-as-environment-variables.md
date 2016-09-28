As discussed previously, we want to avoid keeping our API key in our application's code. Instead, we want to store it on a remote server, or in our case, a local server in a file on your machine. Then, we can load the API key into our Ember app as an **environment variable**. 

This is just like when we were building our apps with Gulp: We created an environment variable called `buildProduction`. We were storing a string in it as a way of telling Gulp whether we are building for production or development. This time, instead of storing a string like the word "production" we will be storing our API key as a string.

## Creating a .env file

Let's start by creating a _.env_ file in the top level of our project directory. Be sure to add it to the _.gitignore_ file before committing. We can do this by adding one line to the default _.gitignore_ content:

<div class="filename">.gitignore</div>  
```
# See http://help.github.com/ignore-files/ for more about ignoring files.

# compiled output
/dist
/tmp

# dependencies
/node_modules
/bower_components

# misc
/.sass-cache
/connect.lock
/coverage/*
/libpeerconnection.log
npm-debug.log
testem.log
.env
```

Now, inside of our new _.env_ file we want to add the following code:

<div class="filename">.env</div>
```
export apiKey="123123123YOURKEYGOESHERE"
```

This is similar to our old _.env_ file, which looked like this:

```
exports.apiKey = "123123123YOURKEYGOESHERE";
```

Notice that the differences here are that instead of using `exports.variableName` we say `export variableName`. **Also, it is very important to not include white spaces around the `=`**. Also, in our new file, there is no need for a semicolon at the end. 

## Loading .env Variables

Next, we need to tell Ember to load that variable by going into _config/environment.js_ and adding this line: `ENV.myApiKey = process.env.apiKEY;` to our development environment. There's already code in this file that we don't want to modify, so just look for the following `if` statement and add the new variable there: 

<div class="filename">config/environment.js</div>
```
  if (environment === 'development') {
    ENV.myApiKey = process.env.apiKey;
  }
```

We would add something similar to the other environments too if we were doing test or production builds. But for the purposes of this class we will only be using the development environment. 

Next, we need to add some code to our route handler so that we can use the API key when fetching data in our model hook. In our Enlightened Citizenry app, this would be in _results.js_. There is one line to add at the beginning which imports our environment variables: 

```
import config from '../config/environment';
```

Then we just need a line where we declare the variable we need. 

```
var key = config.myApiKey;
```

Finally, we can use the new variable when we construct our URL.

<div class="filename">routes/results.js</div>
```
import config from '../config/environment';
import Ember from 'ember';

export default Ember.Route.extend({
  model: function(params) {
    var key = config.myApiKey;
    var url = 'http://congress.api.sunlightfoundation.com/legislators/locate?apikey=' +key+ '&zip=' +params.zip;
    return Ember.$.getJSON(url).then(function(responseJSON) {
      return responseJSON.results;
    });
  }
});
```

Lastly, we just need to restart our server, running one extra command first. This command will load our _.env_ file:

```
$ source .env
$ ember s
```

