In Ember, a **service** is an object that may be inserted and used throughout most portions of an application, no matter which route the user is currently on. It is not destroyed while the application is running unless we explicitly do so ourselves (although refreshing the page will reset the application and with it the service). Services are commonly used for logging, storing authentication tokens, and maintaining a connection to streaming data. Essentially, anything we want to stay around for the life of the application can be handled with a service. 

In this lesson we’ll explore another very common application of services: An online shopping cart. Imagine your favorite online store. There are probably multiple categories users can navigate between (similar to our routes in Ember). Yet, all products you've added to your cart stick around no matter where you're located on the site. 

Let's create a shopping cart service. Like most things in Ember, we can generate it from the command line:

```
$ ember generate service shopping-cart 
```

This will create two files: `app/services/shopping-cart.js` and `tests/unit/services/shopping-cart-test.js`.

We'll include some code to add items to the cart:

<div class="filename">app/services/shopping-cart.js</div>
```js
import Ember from 'ember';

export default Ember.Service.extend({
  items: [],

  add(item) {
    this.get('items').pushObject(item);
  }
});

```

Here, we’re doing two things: We create an array called `items` to hold the list of items in our cart. Then, we write an action called `.add()` that accepts an argument, `item`, to add to our cart. This action simply pushes the new item into our service's `items` array.

Now, let's include an "Add to Cart" button to trigger this action. We'll assume our app has a `store` route and template with a model hook that returns all products in our database:

<div class="filename">templates/store.hbs</div>
```hbs
{{#each model as |product|}}
  {{product-tile product=product}}
{{/each}}
```

As you can see, we render a `product-tile` component for each product in the model. Let’s add our “Add to Cart” button to this component:

<div class="filename">templates/components/product-tile.hbs</div>
```hbs
{{product.title}}
{{product.image}}
{{product.description}}
{{product.cost}}

<button {{action ‘addToCart’ product}}>Add to Cart</button>
… 
```

And a corresponding action on the component:

<div class="filename">components/product-tile.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({
  shoppingCart: Ember.inject.service(),

  actions: {
    addToCart(item) {
      this.get('shoppingCart').add(item);
    },
...
```

When we inject our service, we use the camelCase version of the file name (ie: `shopping-cart` becomes `shoppingCart` in the code above). We're also defining an `.addToCart()` action that takes the item as an argument, gets the `shoppingCart` service we’ve injected, and then calls _the service’s_ `.add()` method to push the item to our `items` array in `services/shopping-cart.js`. 

Now, when we hit this button, the product in our store will be added to our cart. Let's make sure we can actually see this shopping cart! We'll create a component to display the shopping cart contents on our store template:

```shell
$ ember g component display-cart
```

Note that you must inject the service wherever you want access to its data. Because we're now displaying `shopping-cart` information on the `display-cart` component, we must also inject it into the component:

<div class="filename">components/display-cart.js</div>
```js
import Ember from 'ember';

export default Ember.Component.extend({
  shoppingCart: Ember.inject.service(),
});
...
```

Now that we have access to`shopping-cart` in our component, we can display the shopping cart's contents in the component's template. We'll display the title and cost in this example; but use whatever object attributes are relevant to your project:

<div class="filename">templates/components/display-cart.hbs</div>
```
{{#each shoppingCart.items as |item|}}
  {{item.title}} -- {{item.cost}}
{{/each}}
```

Now, the contents of our shopping cart should be visible on our `store` template, through the `display-cart` component we're rendering.  Make sure to include code for any actions you'd like to perform here in your cart (removing items, or emptying the entire cart, etc.) in the component's JavaScript file, too!

You can navigate throughout different routes in the application, and the `shopping-cart` items will remain in your cart. However, a hard-refresh or manually resetting the URL will cause the application to reload, therefore re-defining the `items` array on the service as blank and emptying your cart.

## Additional Resources

Check out the  [Ember Documentation on Services](https://guides.emberjs.com/v2.4.0/applications/services/) for more information on Services in general, and examples of additional 'shopping cart' actions. Remember, you can inject services in routes and models, too.
