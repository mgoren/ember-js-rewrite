
**Helpers** give us a tool to add custom display functionality to our templates. They are most often used to take input (often values from models or components) and return display output (often formatted data or other user interface). 

There are many [pre-existing helpers](http://emberobserver.com/categories/helpers-and-utilities) for Ember we can simply install in our project and use, usually with the `ember install helper-name` command. However, there will be instances in which one of these pre-made tools doesn't meet our application’s needs, and we'll have to create custom helpers.

In this lesson, we’ll create a `city-popularity` helper that will display an icon next to cities with more than 5 rentals. Later, we’ll add a `cost` attribute to rentals and create a `rental-cost` helper that displays anywhere from one to four dollar signs next to a rental depending on its cost. 

### {{city-popularity}} helper

Let’s begin by generating files for `city-popularity`:

```shell
$ember g helper city-popularity
```

This will create two new files: `app/helpers/city-popularity.js` and `tests/unit/helpers/city-popularity-test.js`. 

 The name we give the file will be used to call the helper in our templates, similar to a component. But unlike components, helpers do not require a hyphen in the name; using one is simply standard convention for helpers with multiple-word names.

This helper will take a city object and display a Bootstrap glyphicon if the city contains more than five rentals. Let’s add some logic to our helper file to check the number of rentals in a city:

<div class="filename">app/helpers/city-popularity.js</div>
```
import Ember from 'ember';

export function cityPopularity(params) {
  var city = params[0];

  if(city.get('rentals').get('length') >= 5) {
    return Ember.String.htmlSafe('<span class="glyphicon glyphicon-fire"></span>');
  }
}

export default Ember.Helper.helper(cityPopularity);

```
Here, we’re grabbing the first item from the parameters (a city object) and assigning it the variable name `city`. Then, we check if the number of rentals in the city is over five. If so, we display a Bootstrap glyphicon. If not, nothing happens. 

_(Note:  `Ember.String.htmlSafe();` is a method that allows Ember to render our line of HTML. Ember escapes HTML by default to protect against [cross-site scripting](https://en.wikipedia.org/wiki/Cross-site_scripting) vulnerabilities. Without calling `Ember.String.htmlSafe();` the line`<span class="glyphicon glyphicon-fire"></span>` will appear literally on the page, instead of rendering our nice glyphicon. )_

Now, we can call this helper next to each city on our index template:

<div class="filename">app/templates/index.hbs</div>
```
...
<h2> Cities </h2>

<ul>
  {{#each model.cities as |city|}}
    <li>{{city-popularity city}} {{#link-to 'city' city.id}}{{city.name}}{{/link-to}}</li>
  {{/each}}
</ul>
...

```

Let’s make sure 5+ rentals are added to at least one city. Then, if we refresh the page we can see our glyphicon.  You'll notice that despite being called upon each city in the loop, it’s only displaying our icon if the number of rentals is over 5.  

![city popularity icon](//dl.dropboxusercontent.com/s/s4utrk6npinw9p2/Screen%20Shot%202016-02-24%20at%205.23.07%20PM.png?dl=0)

This helper can now be used throughout the application. Try adding it to your `city` template, or `rental-detail` component.

### {{rental-cost}} helper

Now let’s create a helper for rentals. We’ll add a `cost` attribute to the rental model, and create a helper to display either 1, 2, or 3  `$` symbols next to a rental based upon its cost.

First, let’s add the `cost` attribute to our `rental` model:

<div class="filename">app/models/rental.js</div>
```
import DS from 'ember-data';

export default DS.Model.extend({
  owner: DS.attr(),
  city: DS.belongsTo('city', { async: true }),
  type: DS.attr(),
  image: DS.attr(),
  bedrooms: DS.attr(),
  cost: DS.attr()
});

```
Next, we’ll include a cost field in our new rental form:

<div class="filename">app/templates/components/new-rental.hbs</div>
```
{{#if addNewRental}}
  <h1>New Rental</h1>
    <div>
      <form>
        <div class="form-group">
          <label for="owner">Owner</label>
          {{input value=owner id="owner"}}
        </div>

        <div class="form-group">
          <label for="type">Type</label>
          {{input value=type id="type"}}
        </div>

        <div class="form-group">
          <label for="bedrooms">Number of Bedrooms</label>
          {{input value=bedrooms id="bedrooms"}}
        </div>

        <div class="form-group">
          <label for="image">Image URL</label>
          {{input value=image id="image"}}
        </div>

        <div class="form-group">
          <label for="cost">Cost per night</label>
          {{input value=cost id="cost"}}
        </div>

        <button {{action 'saveRental1'}}>Save</button>
      </form>
    </div>
{{else}}
    <button {{action 'rentalFormShow'}}>New Rental</button>
{{/if}}
```

Lastly, let’s make sure the `saveRental1()` action on the `new-rental` component also includes our new `cost `property.  _(We'll also need to include it in any update forms and actions, but we won't explicitly cover that here.)_ We'll also parse the value into an Integer:

<div class="filename">app/components/new-rental.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({
  addNewRental: false,
  actions: {
    rentalFormShow() {
      this.set('addNewRental', true);
    },

    saveRental1() {
      var params = {
        owner: this.get('owner'),
        city: this.get('city'),
        type: this.get('type'),
        image: this.get('image'),
        bedrooms: this.get('bedrooms'),
        cost: parseInt(this.get('cost'))
      };
      this.set('addNewRental', false);
      this.sendAction('saveRental2', params);
    }
  }
});
```
If we add new rentals to the site, we can see they now contain a `cost` property in Firebase:

![rentals in firebase with cost](//dl.dropboxusercontent.com/s/0ud8jm91uj0x031/Screen%20Shot%202016-02-24%20at%2010.56.50%20AM.png)

We'll generate another set of helper files:

```shell
$ember g helper rental-cost
```
This will create `app/helpers/rental-cost.js` and `tests/unit/helpers/rental-cost-test.js`. We'll add some logic to `app/helpers/rental-cost.js` to  gauge how many  ‘$’  to return based on a rental’s cost:

<div class="filename">app/helpers/rental-cost.js</div>
```
import Ember from 'ember';

export function rentalCost(params) {
  var rentalPrice = params[0].get('cost');

  if (rentalPrice >= 150){
    return '$$$$';
  } else if (rentalPrice >= 100){
    return '$$$';
  } else if (rentalPrice >= 50){
    return '$$';
  } else if (rentalPrice <= 49){
    return '$';
  }
}

export default Ember.Helper.helper(rentalCost);


```
Here, we pull the first item in the parameters (a rental), grab its cost attribute and assign it to the variable `rentalPrice`. Then, depending on how high `rentalPrice` is,  we return anywhere from 4 to 1 `$` symbols.

Let’s call our new `rental-cost` helper in the `index` template, and pass it a `rental` object:

<div class="filename">app/templates/index.hbs</div>
```
<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<h2> Available Rentals </h2>

<ul>
  {{#each model.rentals as |rental|}}
    {{rental-tile rental=rental}} {{rental-cost rental}}
  {{/each}}
</ul>
...

```
Now, when we refresh the page, rentals should have price indicators next to their links! 

![super rentals with price helper](//dl.dropboxusercontent.com/s/coytl257zlefuta/Screen%20Shot%202016-02-24%20at%205.43.14%20PM.png?dl=0)

For more on Ember custom helpers visit the [Writing Helpers](http://guides.emberjs.com/v2.3.0/templates/writing-helpers/) section of the Ember guides. 

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/2.0_custom_helpers)**
