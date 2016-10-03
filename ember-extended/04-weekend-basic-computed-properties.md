Every Ember application we’ve created uses models. As you know, each instance of a model contains multiple properties. We can display, save, update and delete models and their properties.

However, there will be instances where we need to perform varying sorts of calculations or logic on these model properties. For instance, we may want to order a list of comments from newest to oldest, take all ratings users have given a movie and average them into one overall rating, or display a user's age based on a birthdate saved in our database. This is where computed properties come into play.

**Computed Properties** allow us to declare functions as properties of an object. They take existing properties, perform logic on them, and return the result(s) when called. They differ from [Helpers](https://www.learnhowtoprogram.com/javascript/ember-js/custom-helpers) in that Helpers assist in altering _user interface_, whereas computed properties _create new properties_ for use within our application. If this is confusing, don't worry, we'll explore multiple examples of computed properties in several lessons this week.

The most basic example of a computed property is simply combining two existing properties of a model into one _new_ property. For example, let's say we want to be able to easily display the full location of a city in our SuperRentals app. This app has a `city` model with the properties `name`, `country` and `rentals`:

<div class="filename">app/models/city.js</div>
```
import DS from 'ember-data';

export default DS.Model.extend({
  name: DS.attr(),
  country: DS.attr(),
  rentals: DS.hasMany('rental', { async: true })
});
```

Our app contains a `city` page. The `city` route contains the following model hook:

<div class="filename">app/routes/city.js</div>
```
import Ember from 'ember';

export default Ember.Route.extend({
  model(params) {
    return this.store.findRecord('city', params.city_id);
  }
...
```

In the corresponding `city` template we include a `city-tile` component and a `new-rental` component:

<div class="filename">app/templates/city.hbs</div>
```
<h1> Welcome to {{model.name}} </h1>

{{city-tile city=model destroyCity="destroyCity"}}

{{new-rental saveRental2="saveRental3" city=model}}
```

The `city-tile` component shows basic information on the city:

<div class="filename">app/templates/components/city-tile.hbs</div>
```
{{city.name}}'s Details

Country: {{city.country}}

Rentals available in {{city.name}}:

<ul>
  {{#each city.rentals as |rental|}}
    <li>{{#link-to 'rental' rental.id}}{{rental.owner}}'s {{rental.type}}{{/link-to}}</li>
  {{/each}}
</ul>

<button {{action 'destroyCity' city}}>Delete</button>

```

`name` and `country` are each properties of the `city` model. But what if we wanted to make a _new_ property that combined both name and country into one single `fullLocation` property we could call _without_ updating our database? This would require a computed property. 

We'll begin by adding a new function to our `city-tile` component. We put this in the properties section of `city-tile.js`, not down in the actions section:

<div class="filename">app/components/city-tile.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({

  fullLocation: Ember.computed('city.name', 'city.country', function() {
    return this.get('city.name') + ', ' + this.get('city.country');
  }),

  actions: {
   ...
```

Here we're declaring a new computed property called `fullLocation`, and instructing Ember to combine both the `name` and `country` properties when `fullLocation` is called. Now, anytime we call `{{fullLocation}}` in `city-tile.hbs`, our city's full address will be displayed as a single property: 

<div class="filename">app/templates/components/city-tile.hbs</div>
```
<h4>Location:  {{fullLocation}}</h4>

Rentals available in {{city.name}}:

<ul>
  {{#each city.rentals as |rental|}}
    <li>{{#link-to 'rental' rental.id}}{{rental.owner}}'s {{rental.type}}{{/link-to}}</li>
  {{/each}}
</ul>

<button {{action 'destroyCity' city}}>Delete</button>
```

Note that the computed property definition in `city-tile.js` mentions `city.name` and `city.country` not only inside the callback function, but also in the `Ember.computed()` argument list. Those first mentions tell Ember to observe those two properties (`city.name` and `city.country`) and automatically update the `fullLocation` computed property whenever they change.


<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/2.1_basic_computed_properties)**
