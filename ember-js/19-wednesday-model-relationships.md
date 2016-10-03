Now, let’s begin implementing a relationship between rentals and reviews.  Ember Data has [three relationship types](http://guides.emberjs.com/v2.0.0/models/defining-models/#toc_one-to-one) to help establish associations between models: One-to-one, one-to-many and many-to-many.  In our property rental application we will create a one-to-many relationship between rentals and reviews: Rentals will have many reviews, and a review will belong to one rental. 

## Updating Models

### Rental Model

We'll alter the `rental` model to contain many reviews by using the `hasMany` keyword:

<div class="filename">app/models/rental.js</div>
```javascript
import DS from 'ember-data';

export default DS.Model.extend({
  owner: DS.attr(),
  city: DS.attr(),
  type: DS.attr(),
  image: DS.attr(),
  bedrooms: DS.attr(),
  reviews: DS.hasMany('review', { async: true })
});
```

### Review Model

Next, we need to add a `rental` attribute on the `review` model to refer to one of our `rental` objects. We'll add the keyword `belongsTo` to the `rental` model:

<div class="filename">app/models/rental.js</div>
```javascript
import DS from 'ember-data';

export default DS.Model.extend({
  author: DS.attr(),
  rating: DS.attr(),
  content: DS.attr(),
  rental: DS.belongsTo('rental', { async: true }),
});
```

The `{ async: true }` property means that if the related model data isn't already loaded, an additional request should be made to Firebase in order to retrieve it. 

## Create Reviews within a Rental

Now we’ll modify our application so that when a review is added it will be stored in a rental's `reviews` attribute. And when we assign a rental to a review, the rental will be stored in the review's `rental` attribute.  

Previously, we allowed our users to have the option to add new reviews from our index. Since a `review` should now belong to a `rental`, we want to ensure a `rental` is chosen _in advance_ of saving a new `review`.

We’ll allow users to view reviews on the index, but they'll need to select a rental before they can add a new one. We can simply move the `new-review` component that we already created from our `index` template into our `rental` template. We will also pass it the current model as the `rental` object when we render it:

<div class="filename">app/templates/rental.hbs</div>
```html
<h2>More information about {{model.owner}}'s {{model.type}}:</h2>

{{update-rental rental=model update="update"}}

{{rental-detail rental=model destroyRental="destroyRental"}}

{{new-review saveReview="saveReview" rental=model}}
```

(Be sure also to remove the `new-review` component link from the index template.)

### Refactoring the `new-review` Component

We don't need to change anything on the `new-review` component template (_hbs_ portion). In the component javascript we just need to attach the rental to the new review:

<div class="filename">app/components/new-review.js</div>
```javascript
import Ember from 'ember';

export default Ember.Component.extend({
  addNewReview: false,
  actions: {
    reviewFormShow() {
      this.set('addNewReview', true);
    },
    saveReview() {
     var params = {
       author: this.get('author'),
       rating: this.get('rating'),
       content: this.get('content'),
       rental: this.get('rental')
     };
     this.set('addNewReview', false);
     this.sendAction('saveReview', params);
   }
  }
});
```

We can gather the `rental` because we provided the `new-review` component access to the current `rental` object when we said `rental=model` in the line `{{new-review saveReview="saveReview" rental=model}}`.

When we submit our new review form, the action to save a new review will now be processed by the `rental` route rather than the `index` route. We need to include the following in the `actions` section of the rental route handler:

<div class="filename">app/routes/rental.js</div>
```javascript
...
    saveReview(params) {
      var newReview = this.store.createRecord('review', params);
      var rental = params.rental;
      rental.get('reviews').addObject(newReview);
      newReview.save().then(function() {
        return rental.save();
      });
      this.transitionTo('rental', rental);
    }
...
```

(Be sure also to remove the old _saveReview_ action from the index route handler, since it will no longer be used.)

### Saving Both Sides of a Relationship

When we save a new child record (review) to a parent record (rental), we **must save both sides of the relationship**.  In the `saveReview()` action, we first identify the new `review` object and the `rental` it will belong to.  Then, we add the new `review` to the `reviews` attribute of our current `rental` using the `.addObject();` method.  Next, we save the new `review` and specify to _only_ save the `rental` _after_ the review has been successfully saved by using `.then();`.  

Line-by-line, a plain English translation of the above `saveReview()` action would read as follows:

_Create a new review with the information from our parameters, save it to the database, and call it "newReview". <br>
Refer to the rental in those parameters as "rental". <br>
Retrieve the list of reviews located in "rental", and add "newReview" to that list.<br>
Save "newReview", so it remembers what rental it belongs in.<br>
Wait until "newReview" has finished saving, then save "rental" too, so it remembers it contains "newReview".<br>
Afterwards, take us to the page displaying details for "rental"._

### Asynchronous Routing in Ember

Remember when we discussed [the role of JavaScript promises in an `RSVP.hash`](https://www.learnhowtoprogram.com/javascript/ember-js/one-route-multiple-models)?  Logic in Ember routes is asynchronous, meaning Ember will not wait for one line to successfully complete or return a value before running the next line of code. Since our data is stored remotely in Firebase it takes a few extra moments for our application to save a new object. However, we don't want to run `rental.save();` until `newReview` is successfully saved, or we risk our rental containing details for a review that doesn't actually exist. By using `.then()`, we're forcing Ember to wait until `newReview` is successfully saved before saving `rental`. For more information, check out Ember's documentation on [asynchronous routing](https://guides.emberjs.com/v2.3.0/routing/asynchronous-routing/).

## Displaying a Rental's Reviews

We should probably display the reviews on a rental page. Let's add some code to our `rental-detail` component, which we've already instantiated inside of our `rental` template. Rather than putting all the code directly into the `rental-detail` component, we'll create a `review-tile` component that we can display inside the `rental-detail` component, once for each review.

<div class="filename">app/templates/components/rental-detail.hbs</div>
```html
Located in {{rental.city}}, this {{rental.bedrooms}} bedroom {{rental.type}} is
available by arrangement through {{rental.owner}}.
<br>
<p><img src={{rental.image}} alt={{rental.type}}></p>


<h2> Reviews </h2>
<ul>
  {{#each rental.reviews as |review|}}
    {{review-tile review=review}}
  {{/each}}
</ul>

<button {{action 'delete' rental}}>Delete</button>
```

<div class="filename">app/templates/components/review-tile.hbs</div>
```html
<li>{{review.rating}} - {{review.content}} - by {{review.author}}</li>
```

## Clearing the Database

Moving forward, all **new** reviews we create will be associated with their rental. (Any reviews created _before_ we implemented this relationship will not be associated with a rental, simply because we did not have that capability until now.)  As such, **we need to clear our database because the previous data no longer matches the new format we've defined**.  We can easily delete all Firebase entries by clicking the red **X** that appears when we hover over the name of our database.
