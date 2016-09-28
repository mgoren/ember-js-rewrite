As we continue to develop our Super-Rentals app, let's say we want to add more properties and details to each rental.  We only want users to see this additional information if they select the rental to view more. To do this, we'll make each rental list item on our index a link to a template containing individual rental details.  

## Rental Route

First, we'll generate this new route:

```shell
$ ember g route rental
```

Similar to the routes we've previously generated, this command will create three files: One for the route handler (`routes/rental.js`), one for the template the route handler will render (`templates/rental.hbs`), and a test file. Additionally, the router (`app/router.js`) will automatically add an entry for the new route: `this.route('rental')`.

However, we want our new `rental` route to display details for a _single_ rental object. All previous routes we've constructed return _all_ of an object. 

### Adding a Dynamic Segment

In order to do this, we'll include a  **dynamic segment**.  A dynamic segment is simply a placeholder that may be dynamically updated depending on the circumstances (ie: which link we click to travel to this route). In our case, the dynamic segment we add to the `rental` route will represent the `id` of a given rental in Firebase. Including this specific information will allow us to return only a single rental in the route's model hook.  

We'll add this dynamic segment to our new route's entry in the router, like so: 

<div class="filename">app/router.js</div>
```javascript
import Ember from 'ember';
import config from './config/environment';

const Router = Ember.Router.extend({
  location: config.locationType
});

Router.map(function() {
  this.route('about');
  this.route('contact');
  this.route('rental', {path: '/rental/:rental_id'});
});

export default Router;
```

Now, when the router receives a request matching `/rental/:rental_id` (such as from a link within our application that contains the rental's id) it will map the URL to the `rental` route handler,  `rental.js`, and send a params hash that includes the value of the `:rental_id` segment in the URL.

### Finding Records with Dynamic Segment Information

The rental route handler will then use the `params.rental_id` in the model hook to locate the single rental record we've requested:

<div class="filename">app/routes/rental.js</div>
```javascript
import Ember from 'ember';

export default Ember.Route.extend({
  model(params) {
    return this.store.findRecord('rental', params.rental_id);
  },
});
```
Notice that we're using the method `findRecord()` instead of `findAll()` here. Additionally, `findRecord()` takes two arguments: The type of object ('rental'), and the object's specific id (params.rental_id).  After retrieving the specified rental, the route will render the `rental` template. In this template, we'll begin with the following code:

<div class="filename">app/templates/rental.hbs</div>
```
<h2>More information about {{model.owner}}'s {{model.type}}</h2>

```
When we had multiple records in the model (such as on the index route), calling `model` returned an array of rental objects. We then looped through this array to display details about each rental.  However, since the model in our `rental` route represents a _single_ record, we can access all of its by calling `model` directly (ie: `model.owner`, `model.type`, etc).  

## `rental-detail` Component

We'll also generate another component to display details about this model:

```
$ ember g component rental-detail
```

And render this component on our `rental` template, defining the component's `rental` variable as the current `model`:

<div class="filename">app/templates/rental.hbs</div>
```javascript
<h2>More information about {{model.owner}}'s {{model.type}}</h2>
{{rental-detail rental=model}}

```

## Refactoring

Now that we have a special route to display individual rental details, it's safe to assume that most user interactions with a rental will occur on this route. In addition to displaying rental details, let's also move our `update()` and `delete()` actions into this route, instead of the index. 

In the `rental-detail` component's template, we'll include the following: 

<div class="filename">app/templates/components/rental-detail.hbs</div>
```javascript
Located in {{rental.city}}, this {{rental.bedrooms}} bedroom {{rental.type}} is
available by arrangement through {{rental.owner}}.
<br>
<p><img src={{rental.image}} alt={{rental.type}}></p>

<button {{action 'delete' rental}}>Delete</button>
```

Additionally, we'll add our `delete()` action to the JavaScript file of the `rental-detail` component, and remove it from the `rental-tile` component:

<div class="filename">app/components/rental-detail.js</div>
```javascript
import Ember from 'ember';

export default Ember.Component.extend({
  actions: {
    delete(rental) {
      if (confirm('Are you sure you want to delete this rental?')) {
        this.sendAction('destroyRental', rental);
      }
    }
  }
});

```


And, like any component action, we'll need to pass the action through the template in order to reach the route handler by declaring `destroyRental = "destroyRental"`.  

Let's also move our `update-rental` component from `rental-tile` to the rental template, making sure to pass the `update-rental` component `model`: 

<div class="filename">app/templates/rental.hbs</div>
```
<h2>More information about {{model.owner}}'s {{model.type}}:</h2>

{{update-rental rental=model update="update"}}

{{rental-detail rental=model destroyRental="destroyRental"}}

```

Now that `destroyRental()` and `update()` actions are occurring in the rental route, we can move these actions and any corresponding code from `routes/index.js` to `routes/rental.js`. The `update()` action in the `rental-tile` component may also be removed, as we're now updating here on the rental template. Our files should now look as follows: 

<div class="filename">app/routes/rental.js</div>
```javascript
import Ember from 'ember';

export default Ember.Route.extend({
  model(params) {
    return this.store.findRecord('rental', params.rental_id);
  },
  actions: {
    update(rental, params) {
      Object.keys(params).forEach(function(key) {
        if(params[key]!==undefined) {
          rental.set(key,params[key]);
        }
      });
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
   }
  }
});

```

<div class="filename">app/templates/components/rental-tile.hbs</div>
```
<li>
  {{#link-to 'rental' rental.id}}{{rental.owner}}'s {{rental.type}} in {{rental.city}} {{/link-to}}
  {{#if isImageShowing }}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}} ></p>
  {{else}}
    <button {{action 'imageShow'}} >Show image</button>
  {{/if}}
</li>

```

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
    }
  }
});

```

<div class="filename">app/templates/index.hbs</div>
```
<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<ul>
  {{#each model as |rental|}}
     {{rental-tile rental=rental}}
  {{/each}}
</ul>

{{new-rental save2="save3"}}

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}

```
## Linking to Routes with Dynamic Segments

Finally, now that everything is set up,  we can add links to our new `rental` route on our `rental-tile` component on the index. (While here, let's also remove the "Delete" button, if you haven't already, since rentals are now being deleted from `rental-detail`):

<div class="filename">app/templates/components/rental-tile.hbs</div>
```
<li>
  {{#link-to 'rental' rental.id}}{{rental.owner}}'s {{rental.type}} in {{rental.city}} {{/link-to}}
  {{#if isImageShowing}}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}}></p>
  {{else}}
    <button {{action 'imageShow'}}>Image</button>
  {{/if}}
</li>
```

Clicking any of our new rental links will now send a GET request with the URL pattern: `/rental/<unique id assigned from Firebase>.` This id will be visible in the URL of the new page, and the model hook on our `rental` route will utilize this id to return details for only the specific rental we selected. 

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.6_dynamic_routing_find_record)**
