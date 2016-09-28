Ember implements the idea of "convention over configuration" in its design, opting to minimize the amount of configuration necessary to get an application up and running.  Instead, it relies on consistent naming convention to match corresponding portions of the application. 

To do this, a number of peripheral tasks to application development have been streamlined, such as testing, compiling, and serving files.  This allows Ember developers to focus on building apps rather than building the support structures that make them run. 

Ember includes a _command line interface_ called Ember CLI with helpful tools that are immediately available at our fingertips!

## Ember Help

In the terminal, a quick `ember --help` will display the commands available.  For more information on a specific command, type `ember help command-name`.  This shows us the details of how and when the command is used.

## Creating a New Ember App

To start our new project called `super-rentals`, we'll navigate to the directory we'd like our app located in (most likely the Desktop) and use the `new` command:

```shell
$ ember new super-rentals
```

## The Ember CLI Directory Structure

The `new` command generates a project structure with the following files and directories:

```text
|--app
|--bower-components
|--config
|--dist
|--node_modules
|--public
|--tests
|--tmp
|--vendor

bower.json
ember-cli-build.js
package.json
README.md
testem.json
```

Let's take a brief look at the anatomy of an Ember application:

**app**: This is where folders and files for models, components, routes, templates and styles are stored. The vast majority of our coding happens in this folder.

**bower-components / bower.json**: **bower** is a dependency management tool.  It is used to manage front-end plugins and component dependencies (HTML, CSS, JavaScript, etc).  All bower components are installed in the bower-components directory.  If we open `bower.json`, we see the list of dependencies that are installed by Ember automatically. These include Ember, jQuery, Ember Data and QUnit (for testing). If we add additional front-end dependencies, like `bootstrap`, they will also be listed here and their files added to the `bower-components` directory.  

**config**: Contains the _environment.js_ file which lists environmental settings and configurations for our app. We'll look at this more closely later.

**dist**: When an app is built for deployment, the output files will reside here.

**node_modules / package.json**: This directory and file are from **npm**.  _npm_ is the package manager for Node.js. Ember is built with Node and uses a variety of Node.js modules for operation. The _package.json_ file comes pre-loaded with a list of current packages Ember requires. Any Ember-CLI add-ons you install will also show up here. Packages listed in `package.json` are installed in the node_modules directory.

**public**: This directory contains assets such as images and fonts.

**vendor**: This directory is where any front-end dependencies NOT managed by bower reside.

**tests / testem.json**: Contains automated tests for our app. Ember CLI's test runner **testem** is also configured in `testem.json`.

**tmp**: Temporary files live here.

**ember-cli-build.js**: Behind the scenes, Ember CLI uses a tool called Broccoli to compile our code.  This file contains settings for how Broccoli should build our app.

## ES6 Modules

If you take a look at an Ember application file (We'll use `app/router.js` as an example), you'll notice some syntax that may be unfamiliar to you. 

<div class="filename">app/router.js</div>
```javascript
import Ember from 'ember';
import config from './config/environment';

const Router = Ember.Router.extend({
  location: config.locationType
});

Router.map(function() {
});

export default Router;
```

Ember CLI uses [ECMAScript 6](http://www.ecma-international.org/ecma-262/6.0/) (or ES6 for short) modules to organize application code. 
For example, the line `import Ember from 'ember';` gives us access to the actual Ember.js library as the variable `Ember`. And the `import config from './config/environment';` line provides access to our app's configuration data as the variable `config`. The line `const Router = Ember.Router...`. defines a constant called `Router`. (A [constant](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) is essentially a variable whose value cannot be re-assigned). At the end of the file, `export default Router;` makes this `Router` constant available to other parts of the application, similar to the fashion we've exported modules in previous weeks. 

## Starting the Ember Development Server

Once we have a new project in place, we can confirm everything is working by launching the Ember development server:

```shell
ember server
```

or, for short:

```shell
ember s
```

If we navigate to `localhost:4200`, we'll see a welcome message.
