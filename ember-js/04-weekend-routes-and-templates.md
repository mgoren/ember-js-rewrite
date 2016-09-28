We will start our Super Rentals site with just a few static pages: A homepage, an about page and a contact page.  Before we begin, let's consider what our application will look like from the user's perspective:

![super rentals homepage screenshot](https://www.dropbox.com/s/1z3ytzvjkw2v1bt/ember-super-rentals-index.png?raw=1)

Users first arrive at a home page displaying all available rentals. From here, they may navigate to the about and contact pages using links below the rental list.

Before we build these first three pages, we'll need to remove the contents of the `app/templates/application.hbs` file (_if it exists_),  leaving only the `{{outlet}}` line of code. We'll discuss the role of `application.hbs` more after our site has a few pages. You can ignore this step if you are using version 2.6 or later of ember-cli, since it no longer creates an _application.hbs_ file when you create a new project.

## An About Route

Now, let's start by creating our "about" page. Remember, _convention over configuration_.  When the URL path `/about` is loaded, the **Router** will map the URL to the **route handler** of the same name, _about.js_.  The route handler will then load the corresponding  _about.hbs_ **template**.

If we run `ember help generate`, we see a variety of tools come with Ember for automatically generating various files and resources.  We'll use the route generator to build our `about` route:

```shell
$ ember generate route about
```

or for short,

```shell
$ ember g route about
```

We can see what actions are taken by the generator:

```shell
version: 2.4.2
installing route
  create app/routes/about.js
  create app/templates/about.hbs
updating router
  add route about
installing route-test
  create tests/unit/routes/about-test.js
```
Three new files are created: One for the route handler, one for the template the route handler will render, and a test file.  The router is also amended.

When we open the router, we can see that the generator has mapped a new _about_ route for us. This route will load the `about` route handler.

<div class="filename">app/router.js</div>
```app/router.js
import Ember from 'ember';
import config from './config/environment';

const Router = Ember.Router.extend({
  location: config.locationType
});

Router.map(function() {
  this.route('about');
});

export default Router;
```

By default, the `about` route handler loads the `about.hbs` template. We don't need to add anything to the `app/routes/about.js` file we just created for the `about.hbs` template to render automatically.

With routing in place, we can begin coding our template. When we open `about.hbs` we see it currently contains one line: `{{outlet}}`. This is where something called a nested route would render if we had one. (Don't worry about this right now, we'll explore `{{outlet}}` tags in more detail later on when we address application templates and nested routes.)

 We can simply remove this `{{outlet}}` tag add some HTML describing our site to the `about` page:

<div class="filename">app/templates/about.hbs</div>
```javascript
<h2>About Super Rentals</h2>

<p>The Super Rentals website was created to explore the wonderful world of Ember.js. This project allows us to imagine traveling the world while simultaneously traversing the pathways of the Ember framework!</p>
```

If we launch the application by running `ember serve`, and visit `localhost:4200/about` we can see our new page in the browser. Awesome!

This is what the structure of our application currently looks like, and the steps that occur when we travel to the `about` route:

<br><br>

![ember-structure-first-route](//dl.dropboxusercontent.com/s/8ogiki8f3o9gyle/stepbysteproutesember.png)

<br><br>

## A Contact Route

Let's create another static page with contact information.  Once again, we'll start by generating a route, a route handler, and a template with the following command:

```shell
$ ember g route contact
```

We see that our generator has created a `contact` entry in our router,  a corresponding route handler in `app/routes/contact.js`, and a `contact` template. Just like before,  the `contact` route does not require anything additional to load automatically.

In our template,`contact.hbs`, we'll add details for getting in touch with Super Rentals:

<div class="filename">app/templates/contact.hbs</div>
```handlebars
<p>Super Rentals Representatives would love to help you choose a destination or answer any burning questions you may have.</p>

<p>Contact us today!</p>

<p>
  Super Rentals Headquarters<br>
  1212 Test Address Avenue<br>
  TestyMcTestington, OR 97233
</p>

<p>(503) 555-1212</p>

<p>superrentalsrep@superrentals.com</p>
```

Also, we can remove the `{{outlet}}` tag that was automatically included in our _contact.hbs_ file. These tags are used to render something called a nested route, which we will not explore until next week.

Now our second static page is now complete.  If we navigate to `localhost:4200/contact`, we can see our contact page in the browser. If we continue mapping out the structure of our application as it grows, we can see that this second route followed the same pattern as the first:
<br><br>
![tworoutesember](//dl.dropboxusercontent.com/s/6kxdhhosavyucjg/tworoutesember.png)
<br><br>
## Navigating with Links and the {{link-to}} Helper

Of course, we don't want users to have to manually enter URLs to navigate our site. Let's add navigational links at the bottom of each page.  We'll make a "contact" link on the about page and an "about" link on the contact page.

Ember has built-in **helpers** that provide basic functionality, such as linking between routes.  Here is how to use the `{{link-to}}` helper in our code:

<div class="filename">app/templates/about.hbs</div>
```handlebars
<h2>About Super Rentals</h2>

<p>The Super Rentals website was created to explore the wonderful world of Ember.js. This project allows us to imagine traveling the world while simultaneously traversing the pathways of the Ember framework!</p>

{{#link-to 'contact'}}Click here to contact us.{{/link-to}}
```

The helper takes one simple argument: The name of the route this link should take the user to;  in this case, `contact`.  We can refresh the page and see our new link.

![super rentals about page screenshot](//dl.dropboxusercontent.com/s/5iysohx9vr4dety/https://www.dropbox.com/s/zqg9a2dui46o5xd/ember-super-rentals-about.png?raw=1)

Now, let's add a link to our 'about' page on our 'contact' page:

<div class="filename">app/templates/contact.hbs</div>
```handlebars
<p>Super Rentals Representatives would love to help you choose a destination or answer any burning questions you may have.</p>

<p>Contact us today!</p>

<p>
  Super Rentals Headquarters<br>
  1212 Test Address Avenue<br>
  TestyMcTestington, OR 97233
</p>

<p>(503) 555-1212</p>

<p>superrentalsrep@superrentals.com</p>

{{#link-to 'about'}}About{{/link-to}}
```

## An Index Route

Let's also include a homepage to welcome users. Just like our previous pages, we will first generate a new route. Let's call this one "index":

```shell
$ ember g route index
version: 2.4.2
installing route
  create app/routes/index.js
  create app/templates/index.hbs
installing route-test
  create tests/unit/routes/index-test.js
```

Unlike the other two routes we've created, the `index` route is special:  It does _NOT_ require an entry in the Router. You'll notice that generating this route did _not_ alter `app/router.js` at all.  We'll learn more about why this entry isn't required later on. For now, just know the index route is not listed in the router. We can find it at the default path `localhost:4200`.

We'll update our new `index.hbs` template with links to our other two routes:

<div class="filename">app/templates/index.hbs</div>
```handlebars
<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}
```

Now, we have three unique routes: the default index route, the `about` route, and the `contact` route.

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.0_routes_and_templates)**
