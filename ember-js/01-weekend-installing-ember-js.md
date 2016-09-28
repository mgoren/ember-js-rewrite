Getting started with Ember.js is easy. Ember projects are created with the command line build tool Ember CLI (Command Line Interface).  

**Ember CLI should be installed and ready for use on Epicodus computers. The following instructions are meant for installation and configuration on your own personal machine.**

To install Ember CLI on your personal device, you will first need to install:

* Node.js 
* Bower
* Watchman

## OSX
<hr>

On OS X systems, install `Node.js` through `Homebrew` with the following command in your home directory, or visit the [Node website](https://nodejs.org/en/download/) and download the appropriate installer for your operating system:

```
$ brew install node
```

Confirm that `node` and `npm` (node package manager, installed automatically with `Node`) are in place by checking the versions (`Node` should should be 4.0.x or higher, `npm` should be 3.6.x or higher):

```
$ node -v
v6.2.0
$ npm -v
3.10.5
```

_(If you have an older version of `Node` already installed, upgrade through `Homebrew` by running `$ brew upgrade node`.)_

Remaining in your home directory, install `bower` using `npm`:

```
$ npm install -g bower
```

On OSX, also install `watchman` using `Homebrew` _(Watchman does not yet offer a stable Windows version, but Ember-CLI will function without it. For more details, see the Watchman section of the [Ember CLI "Getting Started" Guides](http://ember-cli.com/user-guide/) )._

```
$ brew install watchman
```

Finally, install `ember-cli` using `npm`:

```
$ npm install -g ember-cli
```

You can test that Ember is correctly installed by making an empty test project:

```shell
$ ember new project-name
```

The `new` command will automatically generate a project with the required Ember files and directories. Navigate to the top-level of this project in the command line:

```shell
$ cd project-name
```

And attempt to launch the Ember server:

```shell
$ ember server
```

Navigate to `localhost:4200` in your browser. You should be greeted by text that says "Welcome to Ember.js". 

## Windows / Linux
<hr>


On Windows or Linux systems, download and install `Node.js` from the [Node Website](https://nodejs.org/en/download/), as detailed in [this video](https://www.learnhowtoprogram.com/lessons/installing-node-js)

After installing, confirm you have access to `node` and `npm` (node package manager, installed automatically with `Node`) commands, and the correct versions by checking the versions through the command line, as seen below. `Node` should should be 4.0.x or higher, `npm` should be 3.6.x or higher:

```
$ node -v
v6.2.0
$ npm -v
3.10.5
```

Then, in your home directory, install `bower` using `npm`:

```
$ npm install -g bower
```

Finally, install `ember-cli` using `npm`:

```
$ npm install -g ember-cli
```

You can test that Ember is correctly installed by making an empty test project:

```shell
$ ember new project-name
```

The `new` command will automatically generate a project with the required Ember files and directories. Navigate to the top-level of this project in the command line:

```shell
$ cd project-name
```

And attempt to launch the Ember server:

```shell
$ ember server
```

Navigate to `localhost:4200` in your browser. You should be greeted by a welcome page. 

### Windows Build Times

It's a known issue that build times for Ember projects can take much longer on Windows than Mac or Linux. Two services build into Windows: Windows Defender (meant for protecting against viruses) and Windows Search Index (responsible for indexing files for quick location) monitor all new files created on your machine. When an Ember project is built, many temporary files (most of which we never even see!) are created. As this occurs, these two Windows tools temporarily lock and examine each file, which takes additional time.

If you experience this, know that this is normal, and likely not due to your specific machine. However, there are several things you can do to improve build speeds. 

#### ember-cli-windows 

The additional [ember-cli-windows](https://github.com/felixrieseberg/ember-cli-windows) package improves build times in Windows environments by by disabling the Windows Search Index and Windows Defender tools **only** in the _tmp_ folder in Ember applications. This is considered safe, if only because the _tmp_ files will only ever hold copies and compilations of your existing code. 

Simply run the following commands from the root of each new Ember project:

```
$ npm install ember-cli-windows -g
$ ember-cli-windows
```

The first command installs the package, and the second prompts it to configure Windows Defender and Windows Search for the current project's directory. 

## Additional Resources
<hr>

Further details about the installation process can be reviewed in the [Ember CLI Getting Started documentation](http://ember-cli.com/user-guide/).


