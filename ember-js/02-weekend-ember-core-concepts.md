Before we start writing any Ember code, let's consider some terminology associated with Ember and then walk through a broad overview of how an Ember application works.

Much like Angular, Ember is one of many "client-side MVCs". _MVC_ stands for "model-view-controller".  In Ember 2.0, the acronym, "MTC", or "model-template-component"  would perhaps be more accurate, but despite any differences in naming, the general concept is the same.  Models persist data, templates present the data and user interface, and components handle how the application _behaves_ between user interaction, templates and models.  

Let's further explore how different portions of an Ember application interact. We'll begin by considering how the router and route handlers process a URL request.

## URL, Router and Route Handler

Imagine we are writing a web app for a vacation rental company called Super Rentals that lets users list their properties to rent. At any given time, we should be able to answer questions about the current state like _What rental is the user looking at?_ and _Are they editing it?_ simply by looking at the URL. 

The URL can be set in a few ways:

* The user loads the app for the first time.
* The user changes the URL manually by clicking the back button or by editing the address bar.
* The user clicks a link within the app.
* Some other event in the app causes the URL to change.

No matter how the URL is determined, the first thing that always happens is that the Ember router maps the URL to a route handler. That is, Ember looks for the entry on the Router that matches the current URL, and loads the corresponding route handler. 

The **route handler** then has two primary responsibilities:

* It renders a template.
* It loads a model that is then available to the template.

![Ember core concepts](//dl.dropboxusercontent.com/s/1l8qk61dajbwut1/basic-ember-architecture.png)

## Templates

Ember.js uses **templates** to organize the layout of HTML in an application.

Most templates in an Ember codebase are instantly familiar, and look like any fragment of HTML. For example:

```handlebars
<div>Hi, this is a valid Ember template!</div>
```

Ember templates use the syntax of [Handlebars](http://handlebarsjs.com) templates. Anything that is valid Handlebars syntax is valid Ember syntax. Templates can also display properties provided to them from their context, which is either a component or a route. For example:

```handlebars
<div>Hi {{name}}, this is a valid Ember template!</div>
```

Here, `{{name}}` is a property provided by the template's context. Besides properties, double curly braces (`{{}}`) may also contain helpers and components, which we'll explore a little later.

## Models

**Models** represent persistent state. 

For example, a property rentals application needs to save the details of each rental a user publishes.  A rental would therefore require a model defining its details; most likely called the _rental_ model. A model typically persists information to a web server, although models can be configured to save to anywhere else, such as the browser's Local Storage.

## Components

While templates control how a user interface looks, components control how the user interface _behaves_. 

Unlike the other portions of an Ember application we have discussed so far, **components** consist of **two** files: A template written in Handlebars, and a source file written in JavaScript. The files have the same name, but the template ends in the Handlebars `.hbs` extension, and the source file ends in the `.js` extension. The template defines what the component should _look_ like and the source file controls what _functionality_ that component has. Components are called from within templates, or within the `.hbs` portion of another component.

For example, our property rental application might have a component responsible for displaying all the rentals called `all-rentals`, and another component for displaying an individual rental called `rental-tile`. The `rental-tile` component could define a behavior that allows users to hide and show the image property of the rental. 

Let's see these core concepts in action by beginning our first Ember application - Super Rentals - a site for property owners and renters to come together.




