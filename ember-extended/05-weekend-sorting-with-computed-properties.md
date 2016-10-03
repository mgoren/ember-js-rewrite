Another common usage of computed properties is sorting. Continuing to use our SuperRentals app as an example, let's say we want to display a city's rentals in order of cost.

We display our rentals on a `city-tile` template, within a `city` route:

<div class="filename">app/templates/components/city-tile.hbs</div>
```
<h4>Location:  {{fullLocation}}</h4>

Rentals available in {{city.name}}:

<ul>
  {{#each city.rentals as |rental|}}
    <li>{{#link-to 'rental' rental.id}}{{rental.owner}}'s {{rental.type}}{{/link-to}}</li>
  {{/each}}
</ul>

<button {{action 'destroyCity' city}}>Delete</button>
```

However, by default, this will list rentals in order of creation. What if we want the _least expensive_ rentals displayed at the top? 

In order to sort the rentals by cost, we'll use a computed property on the `city-tile` component by adding the following code:

<div class="filename">app/components/city-tile.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({

  fullLocation: Ember.computed('city.name', 'city.country', function() {
    return this.get('city.name') + ', ' + this.get('city.country');
  }),

  sortBy: ['cost:asc'],
  sortedRentals: Ember.computed.sort('city.rentals', 'sortBy'),

  actions: {
   ...
```

This tells Ember that we'd like to sort our rentals by cost, in ascending order. Now, we can call `sortedRentals` rather than `city.rentals` in our `city-tile` template to view the newly-ordered rentals for our city:

<div class="filename">app/templates/components/city-tile.hbs</div>
```
<h4>Location:  {{fullLocation}}</h4>

Rentals available in {{city.name}}:

<ul>
  {{#each sortedRentals as |rental|}}
    <li>{{#link-to 'rental' rental.id}}{{rental.owner}}'s {{rental.type}}{{/link-to}}</li>
  {{/each}}
</ul>

<button {{action 'destroyCity' city}}>Delete</button>

```

**Note:  Make sure that the rental cost is being saved as an Integer, and not a string in order to sort correctly.**  If numerical values are saved as Strings JavaScript, regardless of framework, will think "76" is larger than "1000" because the first character is larger. You can ensure _all_ new rental costs will be saved as Integers by simply parsing the value when you gather the params: 

<div class="filename">app/components/new-rental.js</div>
```javascript
...
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
...
```

## Additional Resources

For more information on Computed Properties, check out the [Ember Guides](https://guides.emberjs.com/v2.3.0/object-model/computed-properties/).

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/2.2_sorting_with_computed_properties)**
