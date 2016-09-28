Now, let’s begin implementing a relationship between cities and rentals.  Ember Data has [three relationship types](http://guides.emberjs.com/v2.0.0/models/defining-models/#toc_one-to-one) to help establish associations between models: One-to-one, one-to-many and many-to-many.  In our property rental application we will create a one-to-many relationship between cities and rentals: Cities will have many rentals, and a rental will belong to one city. 

## Updating Models

### City Model

We'll alter the `city` model to contain many rentals by using the `hasMany` keyword:

<div class="filename">app/models/city.js</div>
```
import DS from 'ember-data';

export default DS.Model.extend({
  name: DS.attr(),
  country: DS.attr(),
  rentals: DS.hasMany('rental', { async: true })
});
```

### Rental Model

Next, we need to change the `city` attribute on the `rental` model to refer to one of our `city` objects, instead of just containing a string. We'll add the keyword `belongsTo`to the `rental` model:

<div class="filename">app/models/rental.js</div>
```
import DS from 'ember-data';

export default DS.Model.extend({
  owner: DS.attr(),
  city: DS.belongsTo('city', { async: true }),
  type: DS.attr(),
  image: DS.attr(),
  bedrooms: DS.attr()
});
```

The `{ async: true }` property means that if the related model data isn't already loaded, an additional request should be made to Firebase in order to retrieve it. 

## Accessing City Name Through Relationship

Now everywhere we refer to `rental.city` we'll need to instead use `rental.city.name` since with this relationship established a rental's `city` property will now point to the city object rather than to the string that we want to display. But every city object will have a `name` that we can display:

<div class="filename">app/templates/components/rental-tile.hbs</div>
```javascript
<li>{{#link-to 'rental' rental.id}}{{rental.owner}}'s {{rental.type}} in {{rental.city.name}} {{/link-to}}
  {{#if isImageShowing}}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}}></p>
  {{else}}
    <button {{action 'imageShow'}}>Image</button>
  {{/if}}
</li>
```

<div class="filename">app/templates/components/rental-detail.hbs</div>
```javascript
Located in {{rental.city.name}}, this {{rental.bedrooms}} bedroom {{rental.type}} is
available by arrangement through {{rental.owner}}.
<br>
<p><img src={{rental.image}} alt={{rental.type}}></p>

<button {{action 'delete' rental}}>Delete</button>
```

## Important Note

We still have work to do to fully complete the relationship between rentals and cities. Until we complete the steps detailed in the next lesson, `rental.city.name` **won't display the city's name yet**. This is completely normal at this stage in development. Continue following along  to complete the relationship between rentals and cities. 

**Example repo of Super-Rentals application is available at the end of the following lesson.**
