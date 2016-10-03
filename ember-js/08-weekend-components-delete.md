Let's continue working with components. In this lesson we’ll add the functionality to create a brand new rental, and to delete rentals that are no longer available to rent.

## Destroy a record

We'll start by simply adding a delete button beside each rental. This button will bind an action called ‘delete’ to a html button. It will also include the particular rental we want to delete as an argument:   

<div class="filename">app/templates/components/rental-tile.hbs</div>
```html
<li>
  {{rental.owner}}'s {{rental.type}} in {{rental.city}}
  {{#if isImageShowing }}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}}></p>
  {{else}}
    <button {{action 'imageShow'}}>Show image</button>
  {{/if}}
  <button {{action 'delete' rental}}>Delete Rental</button>
</li>
```

We'll also add the `delete` action to the JavaScript file of this same component. Similar to the `imageShow` action we created earlier, this action will run automatically when a user clicks ‘Delete Rental’. (Also, don't forget to add a comma after the closing `}` of the previous action!):

<div class="filename">app/components/rental-tile.js</div>
```javascript
import Ember from 'ember';

export default Ember.Component.extend({
  isImageShowing: false,
  actions: {
    imageShow: function() {
      this.set('isImageShowing', true);
    },
    imageHide: function() {
      this.set('isImageShowing', false);
    },
    delete(rental) {
      if (confirm('Are you sure you want to delete this rental?')) {
        this.sendAction('destroyRental', rental);
      }
    }
  }
});
```

When triggered, the `delete` action will display a confirmation pop-up. If confirmed, it will send the action `destroyRental()` upwards. Remember, **creating, deleting and updating objects in our data store should be handled by the route handler rather than within the component**. This component will not delete the object itself. Instead, it sends notification of our intent to delete this rental upwards through the template and into the route handler by calling `this.sendAction()`. In the `sendAction` function's argument, we provide the name of the function on the template and route handler we’d like to activate (in this case, `destroyRental()`, which we will create momentarily), and the specific rental to provide that action.

Ember follows the paradigm “_Data down, actions up_”. Data must be passed downwards from the route, to the template, and into a component (as we did in previous lessons). Actions must be passed through each ‘level’ of the application in a similar fashion, but in the _opposite_ direction: Actions move _upwards_ from the component, to the template, into the route handler. 

If we review [our diagram of an Ember application](https://dl.dropboxusercontent.com/s/1l8qk61dajbwut1/basic-ember-architecture.png), we can see that the template exists ‘between’ the component and the route handler that will _actually_ remove this rental from our data store. When our component’s `delete()` action is activated, the line `this.sendAction('destroyRental', rental);` sends this action one level “up” to our template.

As such, our template will need to match the action it receives from the component to the appropriate action on the route handler. We do this in the same line that calls our component:

<div class="filename">app/templates/index.hbs</div>
```html
...
<ul>
  {{#each model as |rental|}}
    {{rental-tile rental=rental destroyRental="destroyRental"}}
  {{/each}}
</ul>
...
```

When we say `destroyRental="destroyRental"`, we are mapping the `destroyRental()` action coming up from the component (on the left of `=` ) to the `destroyRental()` action on the route handler. 

Now, we just need to create this action on our route handler: 

<div class="filename">app/routes/index.js</div>
```javascript
import Ember from 'ember';

export default Ember.Route.extend({
  model() {
    return this.store.findAll('rental');
  },

  actions: {
    destroyRental(rental) {
      rental.destroyRecord();
      this.transitionTo('index');
    }
  }
});
```

Now, when we hit a rental's delete button, our action will move upwards from the component, through the template, and into the route handler. The route handler will access our data store and delete the object for us. Finally, we call `this.transitionTo('index')` to return to the index page after a rental is deleted.

To review, the movement of actions from our component, through our template, and into the route handler looks like this: 

![Ember component delete action image](//dl.dropboxusercontent.com/s/uwierwpz4pc8yvg/Copy%20of%20emberstructurenowwithmodels.png)
