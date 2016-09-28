By design, Ember CLI is **backend agnostic** meaning it doesn't know or care where data comes from or is stored. In this lesson, we are going to look at web APIs as a way to provide data to our Ember applications.

API is short for _application programming interface_. Web APIs consist of information our applications may access and utilize by making a request. Essentially, it's a way for our programs to communicate with other applications, or retrieve information from outside resources. 

An example of a web API is the Google Maps API.  When we visit a site with an embedded Google Map, there is logic in the code that makes an HTTP request that tells what map to display, and to receive an HTTP response from Google Maps with the map itself.

### Anatomy of an API GET Request

When a web API GET request is made, most of the necessary information is included in the URL.  For this lesson, we'll use the [Sunlight Foundation Congress API](https://sunlightlabs.github.io/congress/) as an example.

Below is the request URL we might use to get legislator information for zip code 97204:

```
http://congress.api.sunlightfoundation.com/legislators/locate?apikey=0613f3c5dde44d699a1a8c7adb2e6ed7&zip=97204
```

The `?` in the URL is called a **query string**. Information following the `?` is not part of the URL path but instead represent the query parameters being sent with the request. 

In this example, the `apikey` is the first parameter and represents our unique access key provided by the Sunlight Foundation. Many APIs require some level of authentication to manage _who_ is using their data. The second parameter is the zip code which we have manually set to 97024.  Parameters in the query string are separated by the `&` symbol. 

### Anatomy of an API Response

One of the most common data formats used in APIs is **JSON**.  JSON stands for JavaScript Object Notation.  

Here is an example of data returned in a JSON-formatted response:

```
{
     id: "6921874941",
     about: "The Sunlight Foundation is a nonpartisan nonprofit that advocates for open government globally and uses tech to make government more accountable to all.",
     can_post: false,
     category: "Non-Governmental Organization (NGO)",
      description: "The Sunlight Foundation's vision is to use technology to enable more complete, equitable and effective democratic participation. Our overarching goal is to achieve changes in the law to require real-time, online transparency for all government information, with a special focus on the political money flow and who tries to influence government and how government responds. ",
founded: "2006",
     hours: 
     {
          mon_1_open: "09:00",
          mon_1_close: "18:30",
          tue_1_open: "09:00",
          tue_1_close: "18:30",
          wed_1_open: "09:00",
          wed_1_close: "18:30",
     }
}
```

As its name indicates, data in JSON format is organized like a JavaScript object with a series of key-value pairs.  We will be using JSON-formatted APIs to gather data for our Ember models in our applications.

If you're curious to learn more about APIs, visit the Wikipedia entry for [Application programming interface](https://en.wikipedia.org/wiki/Application_programming_interface). If you're curious to learn more about JSON, visit [JSON.org](http://json.org/).
