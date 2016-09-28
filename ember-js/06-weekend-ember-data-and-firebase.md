Using hard-coded model data is great for demonstrating the relationship between models, routes and templates, but is very limited in practice.  As our application grows we will need the ability to create, update and delete rentals. We cannot reasonably do this with a hard-coded array as our model.

However, Ember includes a model data management library called **Ember Data** that will easily provide this functionality.

## Generating Ember Data Models

Let's generate our first Ember Data model:

```shell
$ ember g model rental
```

This will create two files in our application: A model file and a test file:

```shell
version: 2.4.2
installing model
  create app/models/rental.js
installing model-test
  create tests/unit/models/rental-test.js
```

The model file will look something like the following (may vary based on Ember version):

<div class="filename">app/models/rental.js</div>
```javascript
import DS from 'ember-data';

export default DS.Model.extend({

});
```

This file is essentially a 'blueprint' listing out the attributes each of our rentals should contain. If using an older version of Ember, your file may look a little different. If your model file looks different, go ahead and replace the contents of your file with the code above.

Then, we'll use the same attributes we previously used in our hard-coded array of JavaScript objects: Owner, city, type, image, and bedrooms:  

<div class="filename">app/models/rental.js</div>
```javascript
import DS from 'ember-data';

export default DS.Model.extend({
  owner: DS.attr(),
  city: DS.attr(),
  type: DS.attr(),
  image: DS.attr(),
  bedrooms: DS.attr()
});
```

The `DS` in this file stands for "data store".  `.attr()` stands for 'attribute'.  This is a list of object attributes we want to save to our store for each rental we add. When a `DS.Model` is exported, as we see in the line `export default DS.Model.extend({ ... ` it is added to the data store. That is, it's being saved to our database.

## Using Firebase with Ember Data

Ember Data can be configured to save to any persistent data source. We are going to use Firebase databases for our Ember applications. Firebase is a cloud database that stores information in JSON format. Visit [Firebase's website](https://firebase.google.com/) and set up a free account.

### Installing EmberFire

In order for Ember Data to communicate with our Firebase database, we need to configure Ember Data to work with Firebase. We can do this by using an **add-on** called **[EmberFire](https://www.firebase.com/docs/web/libraries/ember/guide.html)**. An add-on is code that extends Ember functionality, but is not part of the core Ember codebase.

Let's install EmberFire from our app directory:

```shell
$ ember install emberfire
```
and we see:

```shell
Installed packages for tooling via npm.
installing emberfire
  create app/adapters/application.js
  install bower package firebase
  not-cached https://github.com/firebase/firebase-bower.git#^3.0.0
  resolved https://github.com/firebase/firebase-bower.git#3.0.4
Installed browser packages via Bower.

EmberFire installed.

CONFIGURATION REQUIRED

Please update config/environment.js with your firebase settings. You can find these at https://console.firebase.google.com/ by clicking the [Add Firebase to your web app] button on the project overview panel.

Example:

// config/environment.js
var ENV = {
  locationType: 'auto',
  // ...
  firebase: {
    apiKey: 'xyz',
    authDomain: 'YOUR-FIREBASE-APP.firebaseapp.com',
    databaseURL: 'https://YOUR-FIREBASE-APP.firebaseapp.com',
    storageBucket: 'YOUR-FIREBASE-APP.appspot.com',
  },


  // if using ember-cli-content-security-policy
  contentSecurityPolicy: {
    'script-src': ''self' 'unsafe-eval' apis.google.com',
    'frame-src': ''self' https://*.firebaseapp.com',
    'connect-src': ''self' wss://*.firebaseio.com https://*.googleapis.com'
  },


Installed addon package.
```

This automatically adds emberfire to `package.json`,  firebase to `bower.json`, and create a new application adapter:

<div class="filename">app/adapters/application.js</div>
```javascript
import FirebaseAdapter from 'emberfire/adapters/firebase';

export default FirebaseAdapter.extend({
});
```

**Adapters** connect our applications to their data stores. This code is creating an adapter specifically meant for connecting to Firebase. It uses Ember's [Service Injection mechanism](https://guides.emberjs.com/v2.3.0/applications/dependency-injection/) to inject it into the application. Don't worry about these specifics yet. For now, just know adapters are responsible for connecting an application to its store, and that we won't often add any code to our adapters, especially when using pre-existing tools like emberfire and Firebase.

### Configuring Firebase

Now, let's configure our Firebase URL and API key. The adapter we just generated will handle connecting to Firebase, but we need to let it know our API key and _where_ our Firebase data store is located. We'll need to log into our Firebase account and create a new project by clicking the _CREATE NEW PROJECT_ button on the Firebase dashboard.  In this example, we are using a Firebase project with the same name as our Ember app: _super-rentals_. We'll add a few lines in the `ENV` variable in `config/environment.js` specifying config info for the app we've just created on Firebase's website. Ember will use this info to locate and communicate with our `super-rentals` Firebase application:

<div class="filename">config/environment.js</div>
```javascript
module.exports = function(environment) {
  var ENV = {
    modulePrefix: 'super-rentals',  # This will differ depending on what you named YOUR app.
    environment: environment,
    baseURL: '/',
    locationType: 'auto',
    EmberENV: {
      FEATURES: {
        // Here you can enable experimental features on an ember canary build
        // e.g. 'with-controller': true
      }
    },

    firebase: {
      apiKey: 'YOUR-API-KEY-HERE', # REPLACE INFO HERE
      authDomain: 'YOUR-FIREBASE-APP.firebaseapp.com', # REPLACE INFO HERE
      databaseURL: 'https://YOUR-FIREBASE-APP.firebaseio.com', # REPLACE INFO HERE
      storageBucket: 'YOUR-FIREBASE-APP.appspot.com', # REPLACE INFO HERE
    },

    APP: {
    ...
...

```

This `firebase` section should be added _inside_ the `ENV` section. Do not erase any other parts of the `ENV` section.

To retrieve the API key and URL for your own Firebase project, from the Firebase console for your project, click the _Add Firebase to your web app_ button. A modal will open that includes your _apiKey_, _authDomain_, _databaseURL_, and _storageBucket_. Copy these into the appropriate spots in your _environment.js_ file, as outlined above.

**After installing and configuring EmberFire, you'll need to restart your server for changes to register.**

### Importing Data to Firebase

Before we add data to our store through our Ember application, we can manually import the data from our hard-coded model hook directly into Firebase.  This will allow us see what it looks like in Firebase and understand how data arrives from Ember. We are _seeding_ the database with dummy data. Eventually we will get rid of that dummy data and instead save data through the app.

First, we'll create a temporary _seed_ file called `rentals.json` in the root directory of our project. It will contain our rental objects in **JSON** format.  JSON stands for JavaScript Object Notation and is a standard format for communicating data between systems. We could actually put the file anywhere (even outside of the project directory), because it is not used by Ember. We are creating this file as a one-time thing simply to upload some hard-coded data to Firebase.

<div class="filename">rentals.json</div>
```text
{ "rentals": [{
    "owner": "Veruca Salt",
    "city": "San Francisco",
    "type": "Estate",
    "bedrooms": 15,
    "image": "https://upload.wikimedia.org/wikipedia/commons/c/cb/Crane_estate_(5).jpg"
  }, {
    "owner": "Mike Teavee",
    "city": "Seattle",
    "type": "Condo",
    "bedrooms": 1,
    "image": "https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfonso_13_Highrise_Tegucigalpa.jpg"
  }, {
    "owner": "Violet Beauregarde",
    "city": "Portland",
    "type": "Apartment",
    "bedrooms": 3,
    "image": "https://upload.wikimedia.org/wikipedia/commons/f/f7/Wheeldon_Apartment_Building_-_Portland_Oregon.jpg"
  }]
}
```

Navigate to the _Database_ section of your Firebase console, and select the _Data_ tab. From here, click the three dots on the right-hand side, and select _Import JSON_ from the options menu:

![Firebase import data screenshot](//dl.dropboxusercontent.com/s/xck5us6br4bh50u/how-to-import-json-in-firebase.png)

Then, select the JSON file you've just created, and hit _Import_:

![importing-data-into-firebase-console](//dl.dropboxusercontent.com/s/6vxqyeus14lmvmi/import-json-ember-2.png)

This uploading process is similar to what will happen when our Ember app sends and receives data from Firebase.  Now, we have our original rentals persisting in our Firebase application.

### Altering Firebase Permissions

Additionally, we'll have to let Firebase know that our application has permission to write to our new database. To do this, we can navigate to the _Rules_ tab at the top of our Firebase console, and update the JSON to read:

```
{
  "rules": {
    ".read": true,
    ".write": true
  }
}

```

![updating-permissions-in-firebase](//dl.dropboxusercontent.com/s/76b5kzwg878edfj/update-firebase-permissions.png)

For now, this will allow _any_ application with our unique Firebase URL to read and write to our database.

## Updating the Model Hook

To use our new data store in our application, we need to update the model hook in our route handler:

<div class="filename">app/routes/index.js</div>
```
import Ember from 'ember';

export default Ember.Route.extend({
  model() {
    return this.store.findAll('rental');
  },
});
```

Here, we specify `this.store` to refer to the Firebase data store we've set up for our application. Then the `findAll` method with the argument `rental` instructs Ember Data to find _all_ records of the type `rental` in the store, and return them to our application.

You may have noticed that the JSON we uploaded to Firebase defines an array of objects labelled rental**s** (plural), whereas the model hook above instructs Ember to return each object of the type rental (singular). Ember and Firebase have built-in functionality to understand pluralization. It will know the "rentals" table in Firebase contains multiple "rental" objects. Generally speaking, your table names in Firebase will be a plural model name, and the model hooks in your routes will refer to the singular model name.

## Diagram

Here is the updated structure of our application, with newly-edited files highlighted:

![ember-structure-with-added-model](//dl.dropboxusercontent.com/s/quemtdvk1z8gov8/emberstructurenowwithmodels.png)

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.2_ember_data_and_firebase)**
