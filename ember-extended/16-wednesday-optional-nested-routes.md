**Nested routes** in Ember allow us to display one route _within_ another. We can create a nested route like this:

<div class="filename">app/router.js</div>
```
Router.map(function() {
  this.route('store', function() {
    this.route('item', { path: '/:item_id' });
  });
});
```

Here, the `item` route we've nested will create the URL `localhost:4200/store/item/:item_id`. 

Now, we need to make sure the template of the "parent" route contains an `{{outlet}}` helper:

<div class="filename">app/templates/store.hbs</div>
```
...
<h3>Store</h3>

{{#each model as |item|}}
  {{#link-to 'store.item' item.id}} {{item.name}} - {{item.price}} {{/link-to}}
{{/each}}

{{outlet}}
```

This is where the template of the child route will be displayed.

Next, we need to name the nested, or 'child' template `app/templates/store/item.hbs`. We'll also insert some code to display details about our item:

<div class="filename">app/templates/store/item.hbs</div>
```
{{item.name}}
{{item.details}}
{{item.price}}

<button {{action ‘add’ product}}>Add to Cart</button>
```

The route handler for this new nested route will also be located in a folder named `store`, with the filepath `app/routes/store/item.js`:

<div class="filename">app/routes/store/item.js</div>
```
...
model(params) {
  return this.store.findRecord('item', params.item_id);
}
...
```

When we link to a route that's nested, we need to include the parent route in the argument to `link-to` - in this case, `store.item` instead of simply `item`. This triggers the router to look for the `store` route first, then look for an `item` route under it. If we simply said `item`, it wouldn't be able to find the `item` route. 

Now, clicking the link we've created in our `store` template should trigger our `item` template to display directly within `store.hbs`, right where we've placed the `{{outlet}}`.

## Optional Videos

If you'd prefer a more visual example of how nested routes work, check out the optional [Treehouse Lesson on Nested Routes in Ember](https://teamtreehouse.com/library/emberjs/routes-and-templates/nested-routes). This video provides a great walkthrough of what nested routes look like in an application, and the additional functionality they offer.  Just note that while functionality is still the same, this video utilizes Ember 1.10.x. There will be several small differences in code between this example, and the manner we use nested routes in Ember 2.x:

1.  In the video, the Router uses `this.resource`. We now use `this.route` (as demonstrated in the code snippet above). 
2.  In the video, `{{#link-to}}` helpers are only given the name of the nested or 'child' route.  In Ember 2.x and above, we need to provide both the parent and child routes in dot notation (ie: `{{#link-to 'store.item' item.id}}`, _not_ `{{#link-to 'item' item.id}}`. 

## Additional Resources

For additional information on nested routes, check out the [Ember Guides on Defining Routes](https://guides.emberjs.com/v2.4.0/routing/defining-your-routes/).
