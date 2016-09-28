In this lesson, we will build a simple application for looking up legislators by zip code. We'll use one of the many government information APIs available through the [Sunlight Foundation](https://sunlightfoundation.com/api/), the Congress API.

## Registering for an API Key

First, we need to register for an API key by visiting the Sunlight Foundation’s [Sign Up](https://sunlightfoundation.com/api/accounts/register/) page. They should immediately email your API key with instructions to activate it.

## Experimenting with API Calls

Next, let's experiment with API requests in the Sunlight Foundation's ["Try it" area](http://tryit.sunlightfoundation.com/congress). Here you can enter your new API key, build sample requests with multiple parameters, and see the JSON data they return.  Selecting the link for "Get /legislators/locate" reveals a number of parameters we can include with an option to "Try it!". Let’s enter a zip code and see what the request returns. 

We could also copy the URL for this request into our browser to see the same thing (make sure to replace the API key with your own unique key from the Sunlight Foundation): 

```
http://congress.api.sunlightfoundation.com/legislators/locate?zip=97204&apikey=0613f3c5dde44d699a1a8c7adb2e6ed7
```

A `GET` request is made to the URL we provide, and JSON-formatted data is returned in the response body:

```
{
  "results": [
    {
      "bioguide_id": "W000779",
      "birthday": "1949-05-03",
      "chamber": "senate",
      "contact_form": "http:\/\/www.wyden.senate.gov\/contact",
      "crp_id": "N00007724",
      "district": null,
      "facebook_id": null,
      "fax": "202-228-2717",
      "fec_ids": [
        "S6OR00110",
        "H0OR03026"
      ],
      "first_name": "Ron",
      "gender": "M",
      "govtrack_id": "300100",
      "icpsr_id": 14871,
      "in_office": true,
      "last_name": "Wyden",
      "lis_id": "S247",
      "middle_name": null,
      "name_suffix": null,
      "nickname": null,
      "oc_email": "Sen.Wyden@opencongress.org",
      "ocd_id": "ocd-division\/country:us\/state:or",
      "office": "221 Dirksen Senate Office Building",
      "party": "D",
      "phone": "202-224-5244",
      "senate_class": 3,
      "state": "OR",
      "state_name": "Oregon",
      "state_rank": "senior",
      "term_end": "2017-01-03",
      "term_start": "2011-01-05",
      "thomas_id": "01247",
      "title": "Sen",
      "twitter_id": "RonWyden",
      "votesmart_id": 27036,
      "website": "http:\/\/www.wyden.senate.gov",
      "youtube_id": "senronwyden"
    },
    {
      "bioguide_id": "B000574",
      "birthday": "1948-08-16",
      "chamber": "house",
      "contact_form": "https:\/\/forms.house.gov\/blumenauer\/webforms\/issue_subscribe.html",
      "crp_id": "N00007727",
      "district": 3,
      "facebook_id": null,
      "fax": "202-225-8941",
      "fec_ids": [
        "H6OR03064"
      ],
      "first_name": "Earl",
      "gender": "M",
      "govtrack_id": "400033",
      "icpsr_id": 29588,
      "in_office": true,
      "last_name": "Blumenauer",
      "middle_name": null,
      "name_suffix": null,
      "nickname": null,
      "oc_email": "Rep.Blumenauer@opencongress.org",
      "ocd_id": "ocd-division\/country:us\/state:or\/cd:3",
      "office": "1111 Longworth House Office Building",
      "party": "D",
      "phone": "202-225-4811",
      "state": "OR",
      "state_name": "Oregon",
      "term_end": "2017-01-03",
      "term_start": "2015-01-06",
      "thomas_id": "00099",
      "title": "Rep",
      "twitter_id": "BlumenauerMedia",
      "votesmart_id": 367,
      "website": "http:\/\/blumenauer.house.gov",
      "youtube_id": "RepBlumenauer"
    },
    {
      "bioguide_id": "M001176",
      "birthday": "1956-10-24",
      "chamber": "senate",
      "contact_form": "http:\/\/www.merkley.senate.gov\/contact\/",
      "crp_id": "N00029303",
      "district": null,
      "facebook_id": "74374931545",
      "fax": "202-228-3997",
      "fec_ids": [
        "S8OR00207"
      ],
      "first_name": "Jeff",
      "gender": "M",
      "govtrack_id": "412325",
      "icpsr_id": 40908,
      "in_office": true,
      "last_name": "Merkley",
      "lis_id": "S322",
      "middle_name": null,
      "name_suffix": null,
      "nickname": null,
      "oc_email": "Sen.Merkley@opencongress.org",
      "ocd_id": "ocd-division\/country:us\/state:or",
      "office": "313 Hart Senate Office Building",
      "party": "D",
      "phone": "202-224-3753",
      "senate_class": 2,
      "state": "OR",
      "state_name": "Oregon",
      "state_rank": "junior",
      "term_end": "2021-01-03",
      "term_start": "2015-01-06",
      "thomas_id": "01900",
      "title": "Sen",
      "twitter_id": "SenJeffMerkley",
      "votesmart_id": 23644,
      "website": "http:\/\/www.merkley.senate.gov",
      "youtube_id": "SenatorJeffMerkley"
    }
  ],
  "count": 3,
  "page": {
    "count": 3,
    "per_page": 20,
    "page": 1
  }
}

…

```

_Note: To view JSON in Chrome nicely, [add the JSONView extension](https://chrome.google.com/webstore/search/jsonview?hl=en-US), or use a site like [JSON Pretty Print](http://jsonprettyprint.com/)._

As you can see, there’s all kinds of information in the response body. The three properties we'll use here are `first_name`, `last_name` and `contact_form`, to display the names and link to the contact URL for each legislator. 

## Implementing API Data in Ember.js

Let's create an Ember CLI app to work with this data:

```
$ ember new legislator-lookup
```

We'll quickly install bootstrap:

```
$ ember install ember-bootstrap
```

Then we'll get started with our first route, the application's homepage:

```
$ ember g route index
```

Let's continue by creating a component. This component will house our form to gather a zip code from the user:

```
$ ember g component lookup-form
```

Now, let's add the code for our form to the `lookup-form` component:

<div class="filename">templates/components/lookup-form.hbs</div>
```
<h1>Legislator Lookup</h1>

<form>
  <div class="form-group">
    <label for="zip">Zip</label>
    {{input value=zip id="zip"}}
  </div>

  <button class="btn btn-primary" {{action 'zipLookup'}}>Submit</button>
</form>
```

The`lookup-form` component will also contain an action that collects the zip code from the form and sends it up through the component, and to the `index` route handler: 

<div class="filename">components/lookup-form.js</div>
```
import Ember from 'ember';

export default Ember.Component.extend({
  actions: {
    zipLookup() {
      var params = {
        zip: this.get('zip')
      };
      this.sendAction('zipLookup', params);
    }
  }
});

```

The `lookup-form` component can now be called in our `index` template. We'll also include our `zipLookup` action:

<div class="filename">template/index.hbs</div>
```
{{lookup-form zipLookup="zipLookup"}}

```

Our application will also require a  corresponding `zipLookup` action on the `index` route:

<div class="filename">routes/index.js</div>
```
import Ember from 'ember';


export default Ember.Route.extend({
  actions: {
    zipLookup(params) {
      this.transitionTo('results', params.zip);
    }
  }
});

```

Instead of hiding and showing results on the `index` route as we've done in previous projects. Here, we're transitioning to a `result` route to display the results of our legislator lookup. When we transition to this route, we need to specify that the _zip_ attribute within our parameters should fill the dynamic segment of the `result` route's URL. Let's create this route now:

```
$ ember g route results
```

And make sure to include a dynamic segment in the route's URL. This dynamic segment will contain the zip code we gathered from the user:

<div class="filename">app/router.js</div>
```
import Ember from 'ember';
import config from './config/environment';

const Router = Ember.Router.extend({
  location: config.locationType
});

Router.map(function() {
  this.route('results', {path: '/results/:zip'});
});

export default Router;

```

Now, when we submit the form in our `lookup-form` component located on the `index`, the component will grab the parameters from the form, pass the `zipLookup()` action and its parameters from the component, through the `index` template, and to the `index` route. Once on the `index` route handler, we will transition to the `results` route using zip code from the form (currently located in `params`) to fill the dynamic portion of the results route's URL.

### Returning API Data in a Model Hook

Now, let's create a model hook on our `results` route. Similar to the way we can return information from Ember Data and Firebase in our model hook, we may also return data from a third-party API. 

The model hook will use those same parameters from the URL to create a `GET` request to the Sunlight Foundation and return the legislators for the zip code the user has entered:

<div class="filename">routes/results.js</div>
```
import Ember from 'ember';


export default Ember.Route.extend({
 model: function(params) {
   var url = 'http://congress.api.sunlightfoundation.com/legislators/locate?apikey=[YOUR_API_KEY_HERE]&zip=' + params.zip;
   return Ember.$.getJSON(url).then(function(responseJSON) {
     return responseJSON.results;
   });
  }
});


```
Let's examine this code more closely. First, the argument for the model's function is `params` which are the parameters from URL. In this case, params includes the dynamic `zip` segment.  

We set a variable `url` to the Sunlight Foundation's Congress API URL including our unique API key and parameters in the query string.

Next, we use the jQuery `getJSON` method to make our API request.

You might think we could do something like:

```javascript
import Ember from 'ember';


export default Ember.Route.extend({
 model: function(params) {
   var url = 'http://congress.api.sunlightfoundation.com/legislators/locate?apikey=[YOUR_API_KEY_HERE]&zip=' + params.zip;
   return Ember.$.getJSON(url);
  }
});

```

**But this will not work.** Remember, logic in Ember routes is _asynchronous_. The browser makes a GET request from `var response = Ember.$.getJSON(url);`, but in the above example, Ember will not wait for the API's response before executing the next line.  At this point, no data has been received, so nothing will actually be returned from the function. As such, your application will have no data to display.

The `getJSON` method runs asynchronously and returns what's called a **promise**. A promise is a standard way of dealing with the results of asynchronous operations. When `getJSON` receives a response from the server, it invokes the callback function passed to `.then()`, and the JSON from the response is assigned to the parameter, which we've called `responseJSON`. Now, in the callback function, we can add the code that should run _after_ the response is received.

(For more information check out the documentation on [getJSON](http://api.jquery.com/jquery.getjson/) and the [`.then()` method](https://api.jquery.com/deferred.then/). )

### Displaying API Data

Moving on, the `results` route will need a template to display our results:

<div class="filename">templates/results.hbs</div>
```
<ul>
  {{#each model as |legislator|}}
    <li> {{legislator.first_name}} {{legislator.last_name}}<br>Contact: <a href=  {{legislator.contact_form}}>{{legislator.contact_form}}</a></li>
  {{/each}}
</ul>

```

Now, when we go to our main page, we should be able to enter a zip and see the corresponding legislators!

As a side note, know that all APIs are different. Some use JSON in their response bodies, others use XML, and some may use different formats entirely. Also, not all APIs are set up to receive requests from web browsers, as the most common way to access an API is from a web server. Sunlight uses the [CORS](http://enable-cors.org/)(Cross-Origin Resource Sharing) standard to enable browsers to use its API; some other APIs use the [JSONP](http://dev.socrata.com/docs/cors-and-jsonp.html) approach for web browsers; and some APIs don't support web browser access at all.
