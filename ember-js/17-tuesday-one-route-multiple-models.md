## Creating a Review Model

Let's build another model for our property rental application: `review`.  We'll want our users to be able to add reviews to a rental.

First, let's generate a new model:

```shell
$ ember g model review
```

and define its attributes:

<div class="filename">app/models/review.js</div>
```
import DS from 'ember-data';

export default DS.Model.extend({
  author: DS.attr(),
  rating: DS.attr(),
  content: DS.attr()
});
```

## Returning Multiple Model Types in One Model Hook

However, in order to display both rentals and reviews on our index page, we will need to update the model hook on the `index` route handler to return two different types of objects:

<div class="filename">app/routes/index.js</div>
```
…
model() {
  return Ember.RSVP.hash({
    rentals: this.store.findAll('rental'),
    reviews: this.store.findAll('review')
  });
},
…
```

### Ember RSVP.Hash and JavaScript Promises

In order to do this, we need an Ember `RSVP.hash`.  **Ember `RSVP.hash`** is a method included in Ember, built with the [RSVP.js Library](https://github.com/tildeio/rsvp.js/). It allows us to load multiple JavaScript **promises** at once. As we briefly discussed in previous weeks, a JavaScript promise is an operation that hasn't completed _yet_, but is expected to in the very near future. Here, ` reviews: this.store.findAll('review')`  and `rentals: this.store.findAll('rental')` are both promises. When Ember first loads your model hook, rentals and reviews are not instantly available. It has to reach out to Firebase and return them. This takes time. These statements are  promises because they will not have completed when Ember _first_ runs this code; it has to wait for these objects to return from Firebase.

A promise can be in one of three states: Pending, fulfilled, or rejected. When the code is first run, the promise is in the pending state. From here, it will either become fulfilled or rejected depending on whether it has successfully completed its goal. This is called _resolving_ the promise.

Grouping multiple promises together in the `RSVP.hash` is beneficial because it merges multiple individual promises into one large promise. The `RSVP.hash` returns a new promise that is only fulfilled when _all_ promises it contains are fulfilled. This prevents asynchronous timing issues in your application by prompting Ember to wait for _all_ necessary objects before rendering your templates and components. Otherwise, it could begin rendering after `rentals` has been returned, but before `reviews` have been successfully fetched from the database.

For more information about how `RSVP.hash` works, visit the [Ember Guides ](https://guides.emberjs.com/v2.3.0/routing/specifying-a-routes-model/#toc_multiple-models).

## Accessing Model Data in an RSVP Hash

Our model now has rentals and reviews which can be referenced in the index template as `model.rentals` and `model.reviews`. This dot notation is necessary because our model now contains two separate arrays of items, and we need to specify which we'd like to return:

<div class="filename">app/templates/index.hbs</div>
```
<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<h2> Available Rentals </h2>

<ul>
  {{#each model.rentals as |rental|}}
    {{rental-tile rental=rental}}
  {{/each}}
</ul>

{{new-rental save2="save3"}}

<h2> All Reviews </h2>

<ul>
  {{#each model.reviews as |review|}}
    <li>{{review.rating}} - {{review.content}} - by {{review.author}}</li>
  {{/each}}
</ul>

<hr>

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}
```

Our `index.hbs` template now shows a list of all rentals and all reviews. However, our Firebase database does not yet contain any reviews. Since we cannot create them directly in our application yet, let's import some directly to Firebase. Let's update our existing _rentals.json_ seed file with the following code and upload it into our Firebase database, just like we did in [this previous lesson](https://www.learnhowtoprogram.com/javascript/ember-js/ember-data-and-firebase).

_(**Note**: Importing a new JSON file will overwrite the current contents of the database. This is normal.)_

<div class="filename">rentals.json</div>

```javascript
{ "rentals": [{
    "owner": "Veruca Salt",
    "city": "San Francisco",
    "type": "Estate",
    "image": "https://upload.wikimedia.org/wikipedia/commons/c/cb/Crane_estate_(5).jpg",
    "bedrooms": 15
  }, {
    "owner": "Mike Teavee",
    "city": "Seattle",
    "type": "Condo",
    "image": "https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfonso_13_Highrise_Tegucigalpa.jpg",
    "bedrooms": 1
  }, {
    "owner": "Violet Beauregarde",
    "city": "Portland",
    "type": "Apartment",
    "image": "https://upload.wikimedia.org/wikipedia/commons/f/f7/Wheeldon_Apartment_Building_-_Portland_Oregon.jpg",
    "bedrooms": 3
  }],
  "reviews": [{
    "author": "Andrea",
    "rating": 4,
    "content": "Did not fall down."
  }, {
    "author": "Alex",
    "rating": 9,
    "content": "Nice view of the volcano!"
  }]
}
```

We should now be able to see all the rentals and reviews listed on our index.

## Next Steps

Now it's your turn! Tomorrow we will begin class by creating a `new-review` component. This component will be rendered in the index template, and will be responsible for creating new `review` objects through our application.

Then, in the next lesson we'll explore creating a one-to-many relationship between these objects, so that reviews can belong to a specific rental.

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals]()**
