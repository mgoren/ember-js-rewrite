Now that we have three static pages, and navigational links between them, let's begin listing available rentals!  We know that our list of rentals will not be static.  Eventually, users will be able to add, update and delete them.  For this reason, we will need a rentals **model** to save information about each rental. To keep things simple, our model will begin as a hard-coded array of JavaScript objects. Later, we'll switch to something called Ember Data and allow users to create and modify their own rentals.

Here's what our homepage will look like when we're done:

![super rentals homepage with rentals list](https://www.dropbox.com/s/rlqonvkr5x7koy3/super-rentals-index-with-list.png?raw=1)

Each time we create a new route, a  **route handler** and template are both generated. The route handler is the _.js_ file that corresponds to the _.hbs_ template file of the same name. The route handler is responsible for loading model data. This data is then available in the corresponding template where it may also be passed to any child components rendered within this template. The route handler is the _only_ part of an Ember application that can access model data.

## Model Data

Let's open `app/routes/index.js` and add some hard-coded data to act as our model:

<div class="filename">app/routes/index.js</div>
```javascript
import Ember from 'ember';

var rentals = [{
  id: 1,
  owner: "Veruca Salt",
  city: "San Francisco",
  type: "Estate",
  bedrooms: 15,
  image: "https://upload.wikimedia.org/wikipedia/commons/c/cb/Crane_estate_(5).jpg"
}, {
  id: 2,
  owner: "Mike TV",
  city: "Seattle",
  type: "Condo",
  bedrooms: 1,
  image: "https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfonso_13_Highrise_Tegucigalpa.jpg"
}, {
  id: 3,
  owner: "Violet Beauregarde",
  city: "Portland",
  type: "Apartment",
  bedrooms: 3,
  image: "https://upload.wikimedia.org/wikipedia/commons/f/f7/Wheeldon_Apartment_Building_-_Portland_Oregon.jpg"
}];

export default Ember.Route.extend({
  model() {
    return rentals;
  },
});
```

(If you haven't used the [ES6 shorthand method definition syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Method_definitions) before, `model()` is the same as writing `model: function()`.)

A method within an Ember class is called a **hook**. Here we've added a **model hook** to our `index` route handler. The model hook returns our hard-coded array of rentals. This data is now available to the route's corresponding `index.hbs` template as the `model` property. That is, when we refer to `model` in the `index.hbs` template, we're referring to this array of rentals in the `index.js` route handler.

Model data in a route handler is available to the templates and components that correspond to _that route_. Remember when we spoke about context in earlier lessons? The `index.hbs` template may now display our model data (an array of rentals) because this data is _within its context_. That is, defined on its corresponding route handler. Take a moment to review the chart in the [Ember Core Concepts](https://www.learnhowtoprogram.com/javascript/ember-js/ember-core-concepts) lesson for a visual representation of this relationship.  

## Displaying Model Data

Now that our _index.js_ route handler has access to our data within its model hook, we can display it in the route handler's corresponding _index.hbs_ template. Know that we will eventually want to display this information in components; _not_ directly on the template. And we will walk through refactoring this code into components in upcoming lessons. But for now, let's confirm our model hook is functioning correctly by displaying our data in the _index.hbs_ template.

We'll use a helper called `{{each}}` to loop through each object in our model and display its information:

<div class="filename">app/templates/index.hbs</div>
```hbs
<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<ul>
  {{#each model as |rental|}}
     <li>{{rental.owner}}'s {{rental.type}} in {{rental.city}}</li>
  {{/each}}
</ul>

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}

```

Here we create an unordered list.  We loop through each object in the model hook defined in the index route handler,  and call it  _rental_.  For each rental, we create a list item and display its owner, type and city.

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.1_models)**
