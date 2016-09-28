**Goal**:  Getting started with Ember comes with a load of new concepts and tools.  Today, your goal is to unpack the weekend's homework and explore how an Ember application is created.  Avoid copying and pasting from the lessons. Focus on understanding the individual portions of an application, what they do, and how they interact.

## Warm Up
<hr>

On a piece of paper, a basic online drawing tool like [draw.io](https://www.draw.io/), or a whiteboard with other pairs, draw out the basic architecture of an Ember application (as seen in the [Ember Core Concepts](https://www.learnhowtoprogram.com/javascript/ember-js/ember-core-concepts) lesson). Label each part of the application clearly. Then, using this visual guide, discuss the following: 

* What does the Router do with the information from the URL? 
* What is the Route Handler responsible for? What 'triggers' it into action, and what action does it then perform?
* What is the difference between a Route Handler and the Router? How do they interact?
* Where are templates located? What do they contain? How does one display a component within a template?
* What portion of the application is responsible for accessing the model? When and where does this occur?
* What are the two different parts of a component? How do they differ? Where do these two parts reside?

## Code
<hr>
### Property Rental Application

Follow along with the weekend homework to create a property rental application. This is an application where property owners may list places for rent,  and renters will be able to find places to stay. (Similar to [AirBnB](https://www.airbnb.com/), or [Vacasa](https://www.vacasa.com/) ). As you build your application, refer back to the map you created during the warmup exercise as necessary. 

To begin, create three basic, static pages that do not require model data:

* Home
* About
* Contact

Next, take your time to work through the following:

* Determine the properties that each rental should have.  Add a list of rentals and their properties to the home page where users can see them when they land on the home page.  (Use data hard-coded in the route handler to begin.)
* Set up a Firebase app to serve as your backend data storage.  Upload your rental data in `json` format.
* Create an Ember Data model for rentals.
* Allow users to click a button to see an image of each rental.
* Let users click the image to hide it again.
* Give users the ability to delete rentals from the list.
* Add a "New Rental" button to allow users to add their rentals to the list.

### Further Exploration

* Create a new model called Announcement that will allow the site administrator to add important messages for users (upcoming sales, weather warnings, and other big exciting news).
* Allow administrators to add and delete announcements. (We are not adding user authentication, so technically the admins and users can see the same information, that's fine for now!)
* Add a button or link on the main page for users to visit these "Important announcements".

## Peer Code Review
<hr>

* Routes are being used to control navigation throughout the application
* Handlebars templates are dynamically displaying data.
* Data is persisting with models, Ember Data and Firebase.
* Components are being called and rendered correctly.

