**Goal**: Using last night's homework as a guide, continue practicing making requests to third-party APIs, as we've covered in prior weeks, paying careful attention to how they fit within an Ember application, and the Ember-specific conventions involved.

## Warm Up
<hr>

* Where is an API request made in an Ember application?
* What is different about making an API request in Ember, as opposed to without it (as we've done in previous weeks)?
* How do we contact an outside API? How do we tell it what kind of information we want returned?

## Code
<hr>
### Enlightened Citizenry - A Resource for Political Information

<p style="color:steelblue; font-style:italic" >"An enlightened citizenry is indispensable for the proper functioning of a republic. Self-government is not possible unless the citizens are educated sufficiently to enable them to exercise oversight."   -Thomas Jefferson </p>

Use the [Sunlight Foundation's Congress API](http://sunlightfoundation.com/api/) featured in last night's homework, or one of their [other political APIs](http://sunlightfoundation.com/api/) to create a website that provides users with a variety of information about legislators, committees, recent and upcoming bills, and/or any other information you and your partner are interested in. 

Before you begin, read the user stories and think through the flow of your application from the user's perspective.  How can the information be logically organized and displayed? Also, spend some time in the Sunlight Foundation's "[try it!](http://tryit.sunlightfoundation.com/congress)" section constructing sample API calls and exploring the types of information available.

Here are some potential user stories to get you started: 

* As a user, I would like to be able to look up contact information for my legislators by zip so that I can contact them.
* As a user, I would like to see the list of committees in the house and in the senate.
* As a user, I would like to select a committee and see its subcommittees.
* As a user, I would like to see a list of recent bills.
* As a user, I would like to be able to select a bill and see the following information about it:
--name and description
--sponsor
--committee
--date introduced
--link to the latest version 
* As a user, I would like to be able to see the name and description of bills that are coming up for votes in the House _in the next week_ (and a count total).
* As a user, I would like to be able to see the name and description of bills that are coming up for votes in the Senate _in the next week_ (and a count total).
* As a user, I would like to be able to link to the sponsor of a bill and see details about the sponsor, including contact information.  (Note:  the sponsor id for a bill is the same as the bioguide id for a legislator).
* As a user, I want to be able to navigate through the site with a navbar.

As you are working through these user stories, you may think of additional features.  Be sure to add them to your user stories list as you go.

### Further Exploration

* use the Open States API to add US State information to your site
* add a feature that uses the Capitol Words API 
* incorporate information about finances and contributions of candidates
* add styling and images

## Peer Code Review
<hr>

* Is an API call successfully returning data to the application?
* Does the application work as expected? 
* Are custom helpers present? If so, are they correctly formatting data?
* Are computed properties present? If so, are they implemented successfully?
