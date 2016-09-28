Let's finish implementing the relationship between our `rental` and `city` models. We’ll modify our application so that when a rental is added it will be stored in a city’s `rentals` attribute. And when we assign a city to a rental, the city will be stored in the rental’s `city` attribute.  

Previously, we wanted our users to have the option to add new rentals from our index. Since a `rental` should now belong to a `city`, we want to ensure a `city` is chosen _in advance_ of saving a new `rental`.

We’ll allow users to view rentals on the index, but they'll need to select a city before they can add a new one. Let's turn each city name into a link to a `city` route (which we will create momentarily) and remove the `new-rental` component from the index template entirely.

![Super Rentals index](//dl.dropboxusercontent.com/s/irjvhhk2h7y2iov/Screen%20Shot%202016-03-29%20at%208.09.12%20PM.png)

Here is our updated `index` template:

<div class="filename">app/templates/index.hbs</div>
```hbs
<h1>Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<h2> Available Rentals </h2>

<ul>
  {{#each model.rentals as |rental|}}
    {{rental-tile rental=rental}}
  {{/each}}
</ul>


<h2> Cities </h2>

<ul>
  {{#each model.cities as |city|}}
    <li>{{#link-to 'city' city.id}}{{city.name}}{{/link-to}}</li>
  {{/each}}
</ul>

{{new-city saveCity="saveCity"}}

<hr>

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}
```

## City Route

On the template, we loop through all cities in our model and create links to each using their `id`.  Let's make sure to generate the new `city` route and template these links will travel to:

```shell
$ ember g route city
```

### Dynamic Segment

This will add `this.route('city')` to our application's router. However, similar to the `rental` route,  we'll need the city `id` in order to find the specific city in our model hook.  We'll add the city's `id` to the route as a dynamic segment:

<div class="filename">router.js</div>
```
import Ember from 'ember';
import config from './config/environment';

var Router = Ember.Router.extend({
  location: config.locationType
});

Router.map(function() {
  this.route('about');
  this.route('contact');
  this.route('rental', {path: '/rental/:rental_id'});
  this.route('city', {path: '/city/:city_id'});
});

export default Router;
```
When city links on the index are clicked, a dynamic URL with the city `id` is sent to the router.  The router then loads the corresponding city route handler. Let's include a model hook on our city route handler to return a specific city:

<div class="filename">app/routes/city.js</div>
```javascript

import Ember from 'ember';

export default Ember.Route.extend({
  model(params) {
    return this.store.findRecord('city', params.city_id);
  }
});
```
The route handler uses the `city_id` from the router to load the correct city in the model hook.  Then, it renders the `city.hbs` template for the individual city (as we did previously for rentals in [this lesson](https://www.learnhowtoprogram.com/javascript/ember-js/dynamic-routing-find-record)).

### Populating the City Template

Let's determine what our `city` page should contain.  For now, we’ll offer two distinct areas:

* The details of our selected city (name, country, rentals available in the city and any future attributes we may care to add)
* A section to add new rentals for the city.

When finished, it will look something like this:

![Super Rentals city view](https://www.dropbox.com/s/xvuzqq7kapw7yfh/super-rentals-city-template.png?raw=1)

Since each of these areas has its own separate purpose we’ll use two different components: A component to display city details called `city-tile` and a `new-rental` component. `new-rental` should look and sound familiar. It was previously located on our index page, where we originally created new rentals.  We can simply move this component from our `index` template into our `city` template. We will also pass it the current model as the `city` object when we render it:

<div class="filename">app/templates/city.hbs</div>
```hbs
<h1> Welcome to {{model.name}} </h1>

{{new-rental save2="save3" city=model}}

{{city-tile city=model destroyCity="destroyCity"}}

```

### Refactoring the `new-rental` Component

The only change necessary in `new-rental.hbs` is removing the `city` field. We will now assign the rental's city attribute to the city whose page we're currently viewing, and no longer need the user to provide it in our form:

<div class="filename">app/templates/components/new-rental.hbs</div>
```hbs
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

        <button {{action 'save1'}}>Save</button>
      </form>
    </div>
{{else}}
    <button {{action 'rentalFormShow'}}>New Rental</button>
{{/if}}
```

The action in the component logic will remain the same:

<div class="filename">app/components/new-rental.js</div>
```javascript
…

save1() {
  var params = {
    owner: this.get('owner'),
    city: this.get('city'),
    type: this.get('type'),
    image: this.get('image'),
    bedrooms: this.get('bedrooms'),
  };
  this.set('addNewRental', false);
  this.sendAction('save2', params);
}
...
```

We can still gather the `city` despite removing that field from the form, because we provided the `new-rental` component access to the current `city` object when we said `city=model` in the line `{{new-rental save2="save3" city=model}}`.

When we submit our new rental form, the action to save a new rental will now be processed by the `city` route. Include the following in the `actions`  hash on your city route:

<div class="filename">app/routes/city.js</div>
```
...

actions: {
    save3(params) {
      var newRental = this.store.createRecord('rental', params);
      var city = params.city;
      city.get('rentals').addObject(newRental);
      newRental.save().then(function() {
        return city.save();
      });
      this.transitionTo('city', params.city);
    }
  }
...
```

## Saving Both Sides of a Relationship

When we save a new child record (rental) to a parent record (city), we **must save both sides of the relationship**.  In the `save3()` action, we first identify the new `rental` object and the `city` it will belong to.  Then, we add the new `rental` to the `rentals` attribute of our current `city` using the `.addObject();` method.  Next, we save the new `rental` and specify to _only_ save the `city` _after_ the rental has been successfully saved by using `.then();`.  

Line-by-line, a plain English translation of the above `saveRental()` action would read as follows:

_Create a new rental with the information from our parameters, save it to the database, and call it "newRental". <br>
Refer to the city in those parameters as "city". <br>
Retrieve the list of rentals located in "city", and add "newRental" to that list.<br>
Save "newRental", so it remembers what city it belongs in.<br>
Wait until "newRental" has finished saving, then save "city'" too, so it remembers it contains "newRental".<br>
Afterwards, take us to the page displaying details for "city"._

### Asynchronous Routing in Ember

Remember when we discussed [the role of JavaScript promises in an `RSVP.hash`](https://www.learnhowtoprogram.com/javascript/ember-js/one-route-multiple-models)?  Logic in Ember routes is asynchronous, meaning Ember will not wait for one line to successfully complete or return a value before running the next line of code. Since our data is stored remotely in Firebase it takes a few extra moments for our application to save a new object. However, we don't want to run `city.save();` until `newRental` is successfully saved, or we risk our city containing details for a rental that doesn't actually exist. By using `.then()`, we're forcing Ember to wait until `newRental` is successfully saved before saving `city`. For more information, check out Ember's documentation on [asynchronous routing](https://guides.emberjs.com/v2.3.0/routing/asynchronous-routing/).

## Clearing the Database

Moving forward, all **new** rentals we create will be associated with their city. (Any rentals created _before_ we implemented this relationship will not be associated with a city, simply because we did not have that capability until now.)  As such, **we need to clear our database because the previous data no longer matches the new format we've defined**.  We can easily delete all Firebase entries by clicking the red **X** that appears when we hover over the name of our database:

![Super Rentals Firebase webshot delete](//dl.dropboxusercontent.com/s/u8ifb3tbhyp8oct/Screen%20Shot%202016-03-30%20at%2012.04.48%20PM.png?dl=0)

If we do not clear out the data store, our app will attempt to use city names in our URLs instead of id numbers. Remember, the city attribute on a rental should now refer to a `city` _object_, not simply a string containing a city name. When we add new cities and rentals through our application, we should now see reference to rental objects _inside_ of city entries, and reference to a city's Firebase-assigned id _within_ a `rental` entry:

![Super Rentals Firebase webshot](https://www.dropbox.com/s/qag6qw5d2o7h7cw/super-rentals-firebase-webshot.png?raw=1)

## Next Steps

Tomorrow we will begin class by creating the `city-tile` component mentioned here to display an individual city's details within the `city.hbs` template.  The `city-tile` component will list all available rentals in a city.

The next lesson's code requires the `city-tile` be present. If you'd like to code along with this lesson, create the `city-tile` component now. Otherwise, read through the lesson now, and code along tomorrow after creating the `city-tile` component in your own Super-Rentals application.

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.10_model_relationships)**
