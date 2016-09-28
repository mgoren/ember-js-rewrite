## Adding a Component 

As a user browses our list of rentals, they'll want additional information to help make a decision. Let's add the ability to hide and show an image for each rental.  To do this, we'll use a component.

We'll generate a `rental-tile` component that will manage the hide/show behavior for each rental. Component names should be kebab-case (all lowercase separated by a dash).  **A dash is _required_ in every component name to avoid possible naming conflicts with HTML elements.**  So `rental-tile` would be an acceptable component name, but `rental` would _not_ be.

We can run the following command to generate a new component: 

```shell
$ ember g component rental-tile
```

And we should see: 

```shell
version: 2.4.2
installing component
  create app/components/rental-tile.js
  create app/templates/components/rental-tile.hbs
installing component-test
  create tests/integration/components/rental-tile-test.js
```

As we discussed earlier, a component consists of two parts: A Handlebars template that defines the _display_ (`app/templates/components/rental-tile.hbs`) and a JavaScript source file that defines how it will _behave_  (`app/components/rental-tile.js`). Despite residing in different locations, these two files are two sides of the same component. 

Since the `rental-tile` component will manage how a user sees and interacts with a rental, we will move the contents of the `{{#each}}` loop on the index into the `rental-tile` template (`app/templates/components/rental-tile.hbs`). Then, we can render the `rental-tile` component inside the loop instead:

<div class="filename">app/templates/index.hbs</div>
```
<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<ul>
  {{#each model as |rental|}}
    {{rental-tile rental=rental}}
  {{/each}}
</ul>

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}
```

Here, we're still looping through each rental in the model. But instead of displaying the rental details directly on the index template, we're rendering our new component by calling `{{rental-tile rental=rental}}`. 

It's important to note that components are _completely isolated_ from their surroundings. They do not automatically have access to models and other information within context of the route in which they are called. Anything a component needs from its surroundings must be passed in manually. In this instance, we are passing the variable `rental` to the component and assigning it the value of `rental` representing the individual rental object in the current iteration of the loop. That is, we're defining the variable `rental` used _in the component's file_ as the model object we're also referring to as `rental` during each iteration of the loop, as defined when we state `{{#each model as |rental|}}`. 

To put it briefly, the `rental` on the left of the  `=`  refers to the variable name used within the _component_ and the `rental` on the right refers to the model object we're assigning this variable to. 

_Note: Declaring "rental=rental" may seem unnecessarily redundant. But by using the same variable name in both the template and component we may consistently use "rental" to refer to the same data whether we're in the template or component files. Any naming convention would work, though.  For example:_

<div class="filename">app/templates/index.hbs</div>
```
…
# Example of alternate variable names. This code will *not* be used in the application we're currently creating together! 

<ul>
  {{#each model as |rental-from-template|}}
    {{rental-tile rental-in-component=rental-from-template}}
  {{/each}}
</ul>
…
```
Again, when passing objects into a component the name on the left of `=` always refers to the variable name used _within the component_. The name on the right refers to the object we're assigning to that variable name. In the above example, assume the component uses the variable name `rental-in-component` within its own template. But here on the index template we call the same rental objects `rental-from-template`. Therefore, we have to declare `rental-in-component = rental-in-template` in order to correctly pass the component the object(s) it needs.

Returning to our project, we'll add code to display rental details in our component:

<div class="filename">app/templates/components/rental-tile.hbs</div>
```handlebars
<li>
  {{rental.owner}}'s {{rental.type}} in {{rental.city}}
</li>
```
If we refresh the page, we should see that the `rental-tile` component is now successfully displaying each rental's details. 

## Hiding and Showing an Image with the `{{action}}` helper

Let's also add functionality to hide and show images for each rental. We'll add the following code to `rental-tile.hbs`: 

<div class="filename">app/templates/components/rental-tile.hbs</div>
```handlebars
<li>
  {{rental.owner}}'s {{rental.type}} in {{rental.city}}
  {{#if isImageShowing }}
    <p><img src={{rental.image}} alt={{rental.type}} ></p>
  {{else}}
    <button>Show image</button>
  {{/if}}
</li>
```

Here, we've added a basic `if` statement in Handlebars: When `isImageShowing` is set to `false`, the user will see a "Show Image"  button.  When `isImageShowing` is `true`, they'll see a picture of the rental.  

The value of `isImageShowing` comes from the component's JavaScript file (`rental-tile.js`).  Since the image will be hidden at first, we will set the property to false:

<div class="filename">app/components/rental-tile.js</div>
```javascript
import Ember from 'ember';

export default Ember.Component.extend({
  isImageShowing: false
});
```

For the button to trigger displaying the image, we will use an **[{{action}} helper](http://guides.emberjs.com/v2.0.0/templates/actions/)** in our template and an **action handler** in our component source file.  

By default, the `{{action}}` helper listens for click events. If we place it on a button, the action we denote will be automatically triggered when that button is clicked:  

<div class="filename">app/templates/components/rental-tile.hbs</div>
```handlebars
<li>
  {{rental.owner}}'s {{rental.type}} in {{rental.city}}
  {{#if isImageShowing }}
    <p><img src={{rental.image}} alt={{rental.type}} ></p>
  {{else}}
    <button {{action 'imageShow'}}>Show image</button>
  {{/if}}
</li>
```

When the 'Show Image' button in `rental-tile.hbs` is clicked,  the `imageShow` action defined in `rental-tile.js` will run, setting the`isImageShowing` value  to `true`. (But we'll code it as false, so it will always be hidden by default until the user clicks the button): 

<div class="filename">app/components/rental-tile.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({
  isImageShowing: false,
  actions: {
    imageShow: function() {
      this.set('isImageShowing', true);
    }
  }
});
```
When this value is true, the `{{#if}}` statement we added in `rental-tile.hbs` will display our image:

<div class="filename">app/templates/components/rental-tile.hbs</div>
```handlebars
<li>
  {{rental.owner}}'s {{rental.type}} in {{rental.city}}
  {{#if isImageShowing }}
    <p><img src={{rental.image}} alt={{rental.type}} ></p>
  {{else}}
    <button {{action 'imageShow'}}>Show image</button>
  {{/if}}
</li>
```

We should also let users hide the image again. We'll add another `{{action}}` helper with the named event `imageHide`.  We can bind`{{action}}` handlers to any HTML element that is clickable.  Here, we'll add it to the image:

<div class="filename">app/templates/components/rental-tile.hbs</div>
```handlebars
<li>
  {{rental.owner}}'s {{rental.type}} in {{rental.city}}
  {{#if isImageShowing }}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}}></p>
  {{else}}
    <button {{action 'imageShow'}}>Show image</button>
  {{/if}}
</li>
```

In our JavaScript component file, we will add code to hide the image by setting the `isImageShowing` property back to `false`:

<div class="filename">app/components/rental-tile.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({
  isImageShowing: false,
  actions: {
    imageShow: function() {
      this.set('isImageShowing', true);
    },
    imageHide: function() {
      this.set('isImageShowing', false);
    }
  }
});
```
Below is our 'mapped out' application with these new features. Follow along with the numbered notes to continue to familiarize yourself with the flow of an Ember application: 

![ember-application-map-with-component-actions](//dl.dropboxusercontent.com/s/snp4t1d5pubz01v/embercomponentactions.png). 

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.3_components_hide_show_image)**
