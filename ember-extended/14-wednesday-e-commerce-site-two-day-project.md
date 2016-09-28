**Goal**: Combine many of the concepts and tools we've learned over the past several weeks to create an E-commerce site. Focus especially on computed properties, helpers and services in anticipation of this week's code review. 

## Warm Up
<hr>

* What is an Ember Service?
* What functionality does an Ember Service offer that components, helpers, and computed properties do not? 
* How does one utilize an Ember Service within an application? Where do we "put" it?
* Where is Ember Service data stored? 

## Code
<hr>
### Online Marketplace

Employers and internship companies have often mentioned to Epicodus how much they appreciate seeing E-Commerce sites in student portfolios. As you can imagine, creating and maintaining online stores is a very common task for development teams around the world. For the next two days we'll focus on creating an excellent portfolio piece for you and your pair to show off to future employers. 

[Nordstrom Rack](https://www.nordstromrack.com/), [Nest](https://store.nest.com/), [Tailor](https://www.tailorbrands.com/), [Eat With](http://www.eatwith.com/), [Spark Plug](https://www.sparkplug.it/), and [Spare Hanger](https://www.sparehanger.com/items) are several examples of online stores that use Ember. You may model your online marketplace off of one of these, a different online marketplace, or create a marketplace all your own. Consider whether you'd like users to be able to list and manage their own items for sale (such as Etsy or Kickstarter), or whether you'd like a single admin to handle listing all products for sale. 

Here are some user stories to get you started. **you're not expected to complete each user story, these are simply possibilities to get you started**: 

* As a user, I would like to add multiple items to my shopping cart.
* As a user, I would like to see the total cost of all items in my cart.
* As a user, I would like to select an item and see a description, cost, and other details.
* As an admin or seller, I would like to list the products I have for sale.
* As an admin or seller, I would like to update or remove products I have for sale.
* As a user, I would like to leave feedback on an item.
* As a user, I would like to leave a rating on an item.
* As a user, I would like to leave a rating for a seller/store (if applicable).
* As a user, I would like to sort ratings/comments from highest-to-lowest, or vis versa.
* As an admin or seller, I would like to see information about all products I have for sale.
* As an admin or seller, I would like to see the date a customer left a piece of feedback or rating. 

### Further Exploration

* Include the ability to mark an item as 'sold'. Allow a seller or admin to view sold products, sort by date sold, and view the total amount of product sold. 
* Use Computed Properties to display an average rating for a seller or product.
* Add functionality to calculate shipping costs based upon location.
* If applicable, integrate Google Maps or another add-on.
* Add the ability for a seller or admin to include a certain quantity of an item. When an item sells out, display it as "sold out". 
* Integrate custom styling. Consider using [Ember bootstrap-sassy](https://github.com/lifegadget/ember-cli-bootstrap-sassy) to include SASS. 

## Peer Code Review
<hr>

* Does the application include at least one computed property?
* Has a service has been implemented successfully?
* Is at least one custom helper is being used to format data?
* Does the application works as expected?
