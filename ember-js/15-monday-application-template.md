The application template is the default template that is rendered when an Ember application is launched.  An unseen _application_ route handler renders the _application_ template:

<div class="filename">app/templates/application.hbs</div>
```
{{outlet}}
```

In the example from our property rental application, we have a single helper element:  `{{outlet}}`. The URL translated by the router to the route handler determines the template that will populate `{{outlet}}`.  As the URL changes, the template filling the `{{outlet}}` changes.  

For example, when we request `/index` from our property rental application, the `{{outlet}}` is filled in with our `app/templates/index.hbs` template.  When we click on "About", the `app/templates/about.hbs` template fills the `{{outlet}}`.

The application template is where headers, footers, nav bars and any other content that should be on every page of the application should go. Let's add a header, footer, and Bootstrap container to our application template:

<div class="filename">app/templates/application.hbs</div>
```
<div class="container">
  <header class = "jumbotron">
    <h1>Super Rentals</h1>
  </header>
    {{outlet}}
  <hr>
  <footer>
    {{#link-to 'contact'}}Our representatives would love to help!{{/link-to}}
  </footer>
</div>

```
As you can see, these elements are now on every page in our application!

<hr>

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for Super-Rentals](https://github.com/epicodus-lessons/ember-super-rentals/tree/1.8_application_template)**
