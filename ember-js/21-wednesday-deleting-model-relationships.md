Now that we have a `rental` attribute on a `review` and a collection of `reviews `as an attribute on a `rental`, what happens if we need to delete a rental?  What happens if we need to delete a review?

When we delete the parent in a one-to-many relationship, we generally want to delete all of its child objects as well. Otherwise we could end up with orphan objects and a messy database!

## Deleting Both Sides of a Relationship

First let's add in the code to delete a review. This part is more or less the same as when we previously walked through deleting a rental, before building the one-to-many relationship. Don't forget to pass the name of the "destroyReview" action down to the component, through any intermediary components. Likewise, don't forget to include in the intermediary `review-detail` component a _destroyReview_ action that simply passes the action up to the route handler.

We need the delete button itself and the code to handle it:

<div class="filename">app/templates/components/review-tile.hbs</div>
```html
<li>{{review.rating}} - {{review.content}} - by {{review.author}}</li>

<button {{action 'delete' review}}>Delete Review</button>
```

<div class="filename">app/components/review-tile.js</div>
```javascript
import Ember from 'ember';

export default Ember.Component.extend({
  actions: {
    delete(review) {
      if (confirm('Are you sure you want to delete this review?')) {
        this.sendAction('destroyReview', review);
      }
    }
  }
});
```

We need to modify the parent _hbs_ and _js_ just to pass the "destroyReview" action up another level to the route handler:

<div class="filename">app/templates/components/review-detail.hbs</div>
```html
Located in {{rental.city}}, this {{rental.bedrooms}} bedroom {{rental.type}} is
available by arrangement through {{rental.owner}}.
<br>
<p><img src={{rental.image}} alt={{rental.type}}></p>


<h2> Reviews </h2>
<ul>
  {{#each rental.reviews as |review|}}
    {{review-tile review=review destroyReview="destroyReview"}}
  {{/each}}
</ul>

<button {{action 'delete' rental}}>Delete</button>
```

<div class="filename">app/components/review-detail.js</div>
```javascript
import Ember from 'ember';

export default Ember.Component.extend({
  actions: {
    delete(rental) {
      if (confirm('Are you sure you want to delete this rental?')) {
        this.sendAction('destroyRental', rental);
      }
    },
    destroyReview(review) {
      this.sendAction('destroyReview', review);
    }
  }
});
```

And, finally, we need the code in the route template and route handler:

<div class="filename">app/templates/rental.hbs</div>
```html
<h2>More information about {{model.owner}}'s {{model.type}}:</h2>

{{update-rental rental=model update="update"}}

{{rental-detail rental=model destroyRental="destroyRental" destroyReview="destroyReview"}}

{{new-review saveReview="saveReview" rental=model}}
```

<div class="filename">app/routes/rental.js</div>
```javascript
...
    destroyReview(review) {
      review.destroyRecord();
      this.transitionTo('index');
    }
...
```

This should take care of deleting the review both in your application, and your Firebase data store. 

Now, let's look at what needs to happen when a rental is destroyed.  When a one-to-many relationship exists between two models,  all of the child records (reviews) must be destroyed _before_ the parent (rental). This prevents **orphans** in the database that reference parent records that are no longer available.  

Let’s update the `rental` route handler action to destroy the child reviews of a rental _before_ destroying the rental:

<div class="filename">app/routes/rental.js</div>
```
...
    destroyRental(rental) {
      var review_deletions = rental.get('reviews').map(function(review) {
        return review.destroyRecord();
      });
      Ember.RSVP.all(review_deletions).then(function() {
        return rental.destroyRecord();
      });
      this.transitionTo('index');
    },
...
```

Here, we define a variable `review_deletions` that iterates over all the rental's reviews, destroying them one by one.  The `Ember.RSVP.all(review_deletions)` waits until all reviews are destroyed before proceeding to _then_ destroy the rental. Similar to the manner we use `Ember.RSVP.hash` when returning [multiple models on one route](https://www.learnhowtoprogram.com/javascript/ember-js/one-route-multiple-models), `Ember.RSVP.all` also 'packages' multiple promises into one. The one primary promise is only resolved when each of its containing promises are fulfilled. This guarantees that Ember won't delete the `rental` until each `review` has been destroyed. 

More information about the RSVP Library Ember utilizes to organize promises can be found in the [RSVP.js README](https://github.com/tildeio/rsvp.js/) and in the [Ember Documentation](http://emberjs.com/api/classes/RSVP.Promise.html) for the `RSVP.Promise` class.

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals]()**
