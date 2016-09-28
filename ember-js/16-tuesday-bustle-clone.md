**Goal**:  Today's goal is to practice implementing the new content from the homework lessons.  Start by adding on to the property rental app and then move into creating a clone of the Bustle.com website.

## Warm Up
<hr>

* What purpose does the application template serve? What would be appropriate to place there? What wouldn't be?
* How are add-ons managed in Ember? How do we install them, and where does Ember keep track of them?
* What does "data down, actions up" mean? What does this look like in Ember?

## Code
<hr>
### Property Rental Application Continued

Before you do anything else, continue building out the property rental app with the features introduced in the homework:

* Add functionality to update rentals, including a new update-rental component.
* Add a rental route to display details of individual rentals on their own page. Include a dynamic path in the Router.
* Install Bootstrap. Add Bootstrap classes throughout the project.
* Add a header, footer, and other content to your _application.hbs_ template.

### Bustle Clone

[Bustle](http://www.bustle.com/) is a news site written with Ember.js. Construct a clone of Bustle by rebuilding some of the site's main features. Focus primarily on construction and functionality. Don't worry about styling the site to look like Bustle until core functionality is implemented. 

* Include a header and navbar that is present on all pages throughout the application.
* Include a homepage with sample headlines, photos, and authors (Hint: You'll probably want to upload some sample stories to Firebase with a JSON file to begin). 
* Users should be able to click on a headline or photo to view the full story. 
* Add an 'admin' portal where site administrators may add new stories, or edit existing stories directly through a form on your site. (But we won't deal with user sign-in and authentication just yet).
* Now, on Bustle's homepage, notice that there is a "carousel" at the top, with arrows on either side; when you click an arrow, a new image moves to the center of the screen. Add this feature. (But don't worry about the scrolling effect.)

### Further Exploration

* Browse [Ember Observer](http://emberobserver.com/) and find additional packages to integrate into your application. There you'll find anything from tools to easily implement [infinite scrolling](http://emberobserver.com/addons/ember-infinity), to packages that provide [icons](http://emberobserver.com/categories/icons), and even sleek ways to add [animated transitions](http://emberobserver.com/addons/liquid-fire) to your app. Just make sure any packages you use are compatible with Ember 2.0!

## Peer Code Review
<hr>

* An application header is being rendered in all areas of the application.
* The application can successfully update/edit stories, and changes are reflected in Firebase.
* A carousel is in place. 
* Routes are controlling navigation throughout the application.
