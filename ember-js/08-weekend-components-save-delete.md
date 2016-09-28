Let's continue working with components. In this lesson we’ll add the functionality to create a brand new rental, and to delete rentals that are no longer available to rent.

## Destroy a record

We'll start by simply adding a delete button beside each rental. This button will bind an action called ‘delete’ to a html button. It will also include the particular rental we want to delete as an argument:   

<div class="filename">app/templates/components/rental-tile.hbs</div>
```javascript
<li>
  {{rental.owner}}'s {{rental.type}} in {{rental.city}}
  {{#if isImageShowing }}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}}></p>
  {{else}}
    <button {{action 'imageShow'}}>Show image</button>
  {{/if}}
  <button {{action 'delete' rental}}>Delete</button>
</li>
```

We'll also add the`delete` action to the JavaScript file of this same component. Similar to the `imageShow` action we created earlier, this action will run automatically when a user clicks ‘Delete’. (Also, don't forget to add a comma after the closing `}` of the previous action!):

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
```javascript
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

## Save a new record

Now, we know how to delete rentals.  Let's allow users to add new ones by clicking a "New Rentals" button underneath the list of rentals on our home page.  

<div class="filename">app/templates/index.hbs</div>
```javascript
<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<ul>
  {{#each model as |rental|}}
    {{rental-tile rental=rental destroyRental="destroyRental"}}
  {{/each}}
</ul>

{{new-rental save2="save3"}}

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}
```
To demonstrate the different steps in the process, we'll use action names with numbers (`save1`, `save2`, and `save3`) to emphasize the individual steps that must occur when passing an action up from a component, through a template and into a route. This is only for example, and is not a special or required naming convention. When we code our next function - updating - we'll use the same name for all of the actions processing the function.  

Here we'll send the `new-rental` component the variable "save2" set to the name of the saving action on the route, "save3".

Let's generate the component that will:

* toggle a "New Rental" button and a new rental form
* handle new rental form submissions

```shell
$ ember g component new-rental
```
In the display, we will set a property, `addNewRental` to `true` or `false`.  When `true`, the form will show.  When `false`, the button will show.  Let's handle each of these possibilities one at a time, starting with our button.

<div class="filename">app/templates/components/new-rental.hbs</div>
```
{{#if addNewRental}}
  <p>Display new rental form - to be replaced with form code soon.</p>
{{else}}
  <button {{action 'rentalFormShow'}}>New Rental</button>
{{/if}}
```

Let's add the behavior for the button:

<div class="filename">app/components/new-rental.js</div>
```javascript
import Ember from 'ember';

export default Ember.Component.extend({
  addNewRental: false,
  actions: {
    rentalFormShow() {
      this.set('addNewRental', true);
    }
  }
});
```

Like hiding and showing the images, we will toggle what is displayed using the `addNewRental` property.  When the button is clicked, the action `rentalFormShow` sets the property value to true to show the form.

Now let's add the form:

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
        <label for="city">City</label>
        {{input value=city id="city"}}
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

Ember provides the [`{{input}}` helper](https://guides.emberjs.com/v2.3.0/templates/input-helpers/) for form inputs that allows us to pass in a number of attributes.  In this example, we are using only the `value` and `id` attributes.

Our submit button has the `save1` action attached to begin the process of saving the new rental added by a user.  

Now, we'll add the component behavior of the `Save` button:

<div class="filename">app/components/new-rental.js</div>
```javascript
import Ember from 'ember';

export default Ember.Component.extend({
  addNewRental: false,
  actions: {
    rentalFormShow() {
      this.set('addNewRental', true);
    },

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
  }
});
```

The `save1` action collects all of the data that was entered in the input fields and sets them as values in a hash of keys with the same names.  So, `this.get('owner')` collects the value entered in the owner input field and assigns it to the variable key `owner`. After each field's value is collected, the form is hidden again by setting the `addNewRental` property to false.

_Note: When a field is not populated, its value will be `undefined`. `Undefined` is not a valid JSON type. Undefined values will prevent the record from being written to Firebase and cause an error.  For fields that may be undefined, add a conditional that sets them to "" or null.  Example: `owner: this.get('owner') ? this.get('owner') : ""`_

Finally, the component emits the action `save2` sending with it the params hash that we just made.  Remember that the component received `save 2` from the index template (and this was set to `"save3"` which we will name the route handler's action for saving.)

The last step is to have the route handler add the new rental to the Ember Data store:

<div class="filename">app/routes/index.js</div>
```javascript
import Ember from 'ember';

export default Ember.Route.extend({
  model() {
    return this.store.findAll('rental');
  },

  actions: {
    save3(params) {
      var newRental = this.store.createRecord('rental', params);
      newRental.save();
      this.transitionTo('index');
    },

    destroyRental(rental) {
      rental.destroyRecord();
      this.transitionTo('index');
    }
  }
});
```
We use the `params` to create a new `rental` record in the store and then save it.  We transition back to our index page and see our new rental in the list.

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.4_components_save_delete)**
