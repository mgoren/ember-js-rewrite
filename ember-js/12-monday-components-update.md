In this lesson we'll implement the ability to update a rental's details. To do this we'll create a new `update-rental` component to be called within our `rental-tile` component.  Remember, Ember relies on the pattern  _"Data down, actions up"_ . This means data must be passed _downwards_ from the route, through the template, and into a component. Conversely, actions must be passed _upwards_  from the component, to the template, and into the route handler.

In this lesson, we will begin at the "bottom" of the application, in a component, and work our way upwards into the route. Our update function will begin when the user clicks an "Update" button. This will display an update form that allows the user to input new rental attributes, like so:

![Update button webshot](https://www.dropbox.com/s/zwssirwrzvnvf90/super-rentals-update-webshot.png?raw=1)

We'll begin by generating a new component:

```shell
$ ember g component update-rental
```
We’ll render this new component within the `rental-tile.hbs` component template, right before the `Delete` button. We'll also include `rental=rental` and `update="update"`in this line, in order to pass the rental object, and the update action we're about to create between the two components:

<div class="filename">app/templates/components/rental-tile.hbs</div>
```
<li>
  {{rental.owner}}'s {{rental.type}} in {{rental.city}}
  {{#if isImageShowing}}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}}></p>
  {{else}}
    <button {{action 'imageShow'}}>Image</button>
  {{/if}}
  {{update-rental rental=rental update="update"}}
  <button {{action 'delete' rental}}>Delete</button>
</li>
```
Let's also add the following code in the `update-rental` component’s template:

<div class="filename">app/templates/components/update-rental.hbs</div>
```
{{#if updateRentalForm}}
  <h4>Update {{rental.owner}}'s {{rental.type}}</h4>
  <div>
    <form>
      <div class="form-group">
        <label for="owner">Owner</label>
        {{input value=owner id="owner" placeholder=rental.owner}}
      </div>

      <div class="form-group">
        <label for="type">Type</label>
        {{input value=type id="type" placeholder=rental.type}}
      </div>

      <div class="form-group">
        <label for="city">City</label>
        {{input value=city id="city" placeholder=rental.city}}
      </div>

      <div class="form-group">
        <label for="bedrooms">Number of Bedrooms</label>
        {{input value=bedrooms id="bedrooms" placeholder=rental.bedrooms}}
      </div>

      <div class="form-group">
        <label for="image">Image URL</label>
        {{input value=image id="image" placeholder=rental.image}}
      </div>

      <button {{action 'update' rental}}>Save</button>
    </form>
  </div>
{{else}}
  <button {{action 'updateRentalForm'}}>Update</button>
{{/if}}
```

In addition to an update form, we've added a property called `updateRentalForm` to toggle the form's display. When this property is `false` we will see an "Update" button. Clicking the button will set the property to true, which will display the form.

Additionally, we’ve bound the action `update` to the “Save” button. When the user fills out the form and hits “Save” the update action in the JavaScript portion of this component will be triggered. Let's write that action now:

<div class="filename">app/components/update-rental.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({
  updateRentalForm: false,
  actions: {
    updateRentalForm() {
      this.set('updateRentalForm', true);
    },
    update(rental) {
      var params = {
        owner: this.get('owner'),
        city: this.get('city'),
        type: this.get('type'),
        image: this.get('image'),
        bedrooms: this.get('bedrooms'),
      };
      this.set('updateRentalForm', false);
      this.sendAction('update', rental, params);
    }
  }
});
```
The `update` action receives the rental as an argument.  Then, we retrieve information from the form, and package it in a `params` hash. **It's important that the variable names we assign these values match the rental model’s attribute names** (ie: `owner`, `city`, `type`,  `image`, and `bedrooms`; the same attributes we defined on our model in `models/rental.js`). If they do not, we will receive errors when assigning these new values to our rental object.

Once the params are gathered, we hide the form by setting `updateRentalForm` back to `false`, and call `this.sendAction('update', rental, params);` to send the `update()` action up one level, along with the rental object we will alter, and the params we've just gathered.

Because `update-rental` is nested within another component, `rental-tile`, `this.sendAction('update', rental, params);` **only sends the  `update()` action up one level**. It is sent from the `update-rental` component to its parent component, `rental-tile`.  As such, the `rental-tile` component will _also_ have to pass the action upward in order for this action to eventually reach the appropriate route. `rental-tile` will need its own `update` action to do this:

<div class="filename">app/components/rental-tile.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({
  isImageShowing: false,
  updateRentalForm: false,
  actions: {
    imageShow() {
      this.set('isImageShowing', true);
    },
    imageHide() {
      this.set('isImageShowing', false);
    },
    update(rental, params) {
      this.sendAction('update', rental, params);
    },
    delete(rental) {
      if (confirm('Are you sure you want to delete this rental?')) {
        this.sendAction('destroyRental', rental);
      }
    }
  }
});
```
**_Note: Don’t forget commas between your actions!_**

To review, the `update()` action in `rental-tile` is triggered when the child component, `update-rental` calls `this.sendAction('update', rental, params);`. This action simply takes the `rental` and `params` objects and continues to pass them upwards to the index template.

Within our index template, we'll include a line to map the `update()` action passed upwards from `rental-tile` to the corresponding `update()` action defined in our index route:

<div class="filename">app/templates/index.hbs</div>
```
<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<ul>
  {{#each model as |rental|}}
    {{rental-tile rental=rental update="update" destroyRental="destroyRental"}}
  {{/each}}
</ul>

{{new-rental saveRental2="saveRental3"}}

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}
```

Finally, we’ll add the route handler’s `update()` action.  Remember, **the route handler is the only portion of our application that should directly access model data**. The route handler will need to know the specific rental to update and the new properties we'd like to save to this rental. Therefore, we'll write this action to take two arguments: The `rental` and `params` objects we've been passing through each level of our application.

Before writing the entirety of this action, let's take a quick look behind the scenes.  We'll place a `debugger;` directly in our new `update()` action:

<div class="filename">app/routes/index.js</div>
```
import Ember from 'ember';

export default Ember.Route.extend({
  model() {
    return this.store.findAll('rental');
  },

  actions: {
    saveRental3(params) {
      var newRental = this.store.createRecord('rental', params);
      newRental.save();
      this.transitionTo('index');
    },

    update(rental, params) {
      debugger;
      rental.save();
      this.transitionTo('index');
    },

    destroyRental(rental) {
      rental.destroyRecord();
      this.transitionTo('index');
    }
  }
});

```

Let’s navigate to the update form, enter new values for `owner`, `type` and `bedrooms`, and submit it.

![Debugger Webshot](https://www.dropbox.com/s/k4lsh8vzqvmja5e/super-rentals-update-debugger-webshot.png?raw=1)

When the application pauses, select the console from the bottom pane of developer tools. We'll type "rental" directly into the console. This will display our current rental object. Using the triangle left of 'Class' will expand its details. Here we can see the `get` and `set` actions for each of the rental's properties.  If we look at the `params` hash we can see the key-value pairs for each of our update form's fields.  Fields that were updated include the value entered by the user.  Fields that were not are listed as `undefined`.  Our updated fields should be `owner`, `type` and `bedrooms`,  as visible in the image above.

Again, the keys in`params` should match the names of the attributes on`rental` model.  We want to update every attribute the user defined to the new value (ie: every value not `undefined`).  In this example, we want the `owner`, `type` and `bedrooms` properties to be updated with the new values, but  `city` and `image` should remain the same, since the user has opted not to update those attributes.

We'll code the rest of our index route’s `update()` action to do this now:

<div class="filename">app/routes/index.js</div>
```
...
update(rental, params) {
  Object.keys(params).forEach(function(key) {
    if(params[key]!==undefined) {
      rental.set(key,params[key]);
    }
  });
  rental.save();
  this.transitionTo('index');
},
...
```

Line-by-line in plain English, our code reads as follows:

_For each key in the `params`, <br>
if it is NOT undefined, <br>
take the rental and set the property that matches the current key, to the value of the current key, <br>
after looping through all of the keys, save the rental,<br>
transition to the index route._<br>

This assures that properties the user has updated will be changed accordingly, but properties the user has _not_ updated will remain the same (instead of being redefined as `undefined`). After all, they didn’t want to change those.

And when we arrive on our index, our rental has been updated!

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.5_components_update)**
