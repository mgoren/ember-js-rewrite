Let's say that we want to be able to add a Google map to the details page for each of our rentals in the Super-Rentals application we've been developing throughout this course.  To do this we'll use the [Google Maps API](https://developers.google.com/maps/documentation/javascript/tutorial).  We will follow the Google Maps API to create an interface between our Ember application and the Google Maps service.

In brief, we will be getting the values for a rental's latitude and longitude and then making a request to Google Maps.  Using those received values, Google Maps will respond with a map object that we can use to display the location of our rental to the user.

To use Google Maps in our application, we need to do two things in advance:

1. Get a Google Developer API key
2. Import the Google Maps library into our project

## Getting a Google Developer API Key

As part of our request to Google Maps, we will need to provide a unique API key for our development project.  To get a key:

Visit the Google Developers Console [here](https://console.developers.google.com/flows/enableapi?apiid=maps_backend&keyType=CLIENT_SIDE&reusekey=true)

1) Select "Create a new project" in the dropdown field and press _Continue_.<br>
2) In the _Name_ field, add the name of your application and press _Create_. (Leave the HTTP referrers field blank.)<br>
![Getting an API key](https://www.dropbox.com/s/l55i06zos41l990/google-maps-api-key.png?raw=1)<br><br>
3) The next window shows the API key that you will use in making calls to the Google Maps API.<br><br>
![Api Key](https://www.dropbox.com/s/sl8hd5cpqrsbwgt/here-is-api-key.png?raw=1)<br><br>
4) Make note of the API key, as you will need it in the next step.

## Importing the Google Maps Library

We are going to create a local copy of the API using the `cURL` utility for file transferring in the terminal.  Since this is not a Node or Bower dependency, the _vendor_ folder is where the files will go.

In our project folder in the terminal, we run:

```shell
$ curl -o vendor/gmaps.js https://maps.googleapis.com/maps/api/js?key=AIzaSyAIRkXC_iZQI4vcDY8Y6B4uvjEKYSSnLuY
```
(This is all one line with a single space between `vendor/gmaps.js` and `https://maps.googleapis.com`.)

Once the file transfer is complete, you can see the _gmaps.js_ file in your project's _vendor_ folder.  When your application is created, Ember will now include the file as a dependency.

The last step is to import it:

<div class="filename">ember-cli-build.js</div>
```
var EmberApp = require('ember-cli/lib/broccoli/ember-app');

module.exports = function(defaults) {
  var app = new EmberApp(defaults, {
  });

  app.import('bower_components/moment/moment.js')
  app.import('vendor/gmaps.js');

  return app.toTree();
};

```

Now, we have everything ready to begin using the Google Maps API in our application.
