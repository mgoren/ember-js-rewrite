**Goals**:  Practice implementing one-to-many relationships between the rentals and reviews from our ongoing Super-Rentals application, and the posts and comments in our two-day Blog project. 

## Warm Up
<hr>

* What are the three primary relationship types in Ember Data? What are their similarities and differences?
* Why do we include `.then()` when saving an object that includes a relationship? What does `.then()` do?
* What does it mean when we say Ember's routes are "_asynchronous_"?

## Code
<hr>

### Property Rental Application

Follow along with the previous night's homework to implement new features and concepts into our Super-Rentals application.

<hr>

### Blog: Day Two
#### _Comments and Model Relationships_

Continue working on the Blog application you and your partner began yesterday. Do not begin working on the functionality listed here until all basic functionality from [yesterday's exercise](https://www.learnhowtoprogram.com/lessons/blog-day-one) is present.  

Allow users to comment on posts. Posts and comments should have a one-to-many relationship. 

Features to include:

* Allow users to navigate to individual posts and add new comments. 
* Give users the opportunity to update the content of their comments.
* Provide the option to delete comments.

### Further exploration

* Include a "Recent Comments" feed on the homepage that displays the newest comments (for any/all blog posts) displayed as links.  Clicking a link should take the user to the post this comment belongs to.
* Add tags to post. Allow users to filter all posts based on tags. 

## Peer Code Review
<hr>

* Users can update existing posts, and changes are reflected in Firebase.
* Posts can be deleted successfully.
* A functioning data relationship is present in the application
* Routes are being used to control navigation throughout the application.
* Data is being manipulated between models and templates using components.
