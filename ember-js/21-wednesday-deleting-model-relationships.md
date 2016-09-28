Now that we have a `city` attribute on a `rental` and a collection of `rentals `as an attribute on a `city`, what happens if we need to delete a city?  What happens if we need to delete a rental?

If we leave our code as-is from previous lessons, deleting a `rental` would result in its parent city's `rentals` attribute retaining an `id` for a rental that _no longer exists_. This would cause issues in display and processing for both models, and result in a messy database!

## Deleting Both Sides of a Relationship

Let's ensure that when we delete a `rental`, we _also_ delete the reference to that `rental` in the `city` it belongs to. We'll update the action to delete a rental in the route. (Also, **make sure the `city-tile` component you were assigned to create in the previous lesson correctly contains an action to delete cities in its JavaScript file, and a button to trigger that action in its Handlebars template**!)

<div class="filename">app/routes/rental.js</div>
```
...
destroyRental(rental) {
  rental.destroyRecord();
  this.transitionTo('index');
}
...
```
This should delete the city both in your application, and your Firebase data store. 

Now, let's look at what needs to happen when a city is destroyed.  When a one-to-many relationship exists between two models,  all of the child records (rentals) must be destroyed _before_ the parent (city). This prevents **orphans** in the database that reference parent records that are no longer available.  

Let’s update the route handler action to destroy the child rentals of a city _before_ destroying the city:

<div class="filename">app/routes/city.js</div>
```
...
destroyCity(city) {
  var rental_deletions = city.get('rentals').map(function(rental) {
    return rental.destroyRecord();
  });
  Ember.RSVP.all(rental_deletions).then(function() {
    return city.destroyRecord();
  });
  this.transitionTo('index');
}
...
```

Here, we define a variable `rental_deletions` that iterates over all the city's rentals, destroying them one by one.  The `Ember.RSVP.all(rental_deletions)` waits until all rentals are destroyed before proceeding to _then_ destroy the city. Similar to the manner we use `Ember.RSVP.hash` when returning [multiple models on one route](https://www.learnhowtoprogram.com/javascript/ember-js/one-route-multiple-models), `Ember.RSVP.all` also 'packages' multiple promises into one. The one primary promise is only resolved when each of its containing promises are fulfilled. This guarantees that Ember won't delete the `city` until each `rental` has been destroyed. 

More information about the RSVP Library Ember utilizes to organize promises can be found in the [RSVP.js README](https://github.com/tildeio/rsvp.js/) and in the [Ember Documentation](http://emberjs.com/api/classes/RSVP.Promise.html) for the `RSVP.Promise` class.

**Again, don't forget to add the delete button to the `city-tile` template file and its corresponding action to the` city-tile` javascript file.**

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.11_deleting_model_relationships)**
