By now we should be familiar with the structure of an Ember project. The majority of our code is located in the `app` folder, which contains a subfolder for each type of Ember resource: `templates`, `components`, `helpers`, `routes`, `services`, etc. 

## Default File Structure

For instance, the `rental` resource in our super-rentals project has the following file structure:

* `app/templates/rental.hbs`
* `app/routes/rental.js`
* `app/models/rental.js`

While they all exist to create, display, and load `rental`s, they reside in three distinctly different places. Similarly, the corresponding`.hbs` and `.js` files for components are also located in two separate places. 

Our `rental-detail` component, for example, uses the following files:

* `app/components/rental-detail.js`
* `app/templates/components/rental-detail.hbs`

Again, they're in two different locations. As Ember projects grow in size, this manner of organization may become less convenient for some developers. Fortunately, there's another organizational structure we may use.

## Pod File Structure

**Pods** are an option in Ember that allows us to organize our application by _feature_ instead of _type_. With pod organization, the file structure for the `rental` resource we mentioned above would look like this:

* `app/rental/template.hbs`
* `app/rental/model.js`
* `app/rental/route.js`

And the `rental-detail` component would have the following file structure in pod format:

* `app/components/rental-detail/component.js`
* `app/components/rental-detail/template.hbs`

### Implementing Pods

In order to utilize pods, we include a `--pod` flag whenever we generate a new files, like so:

```
$ ember generate component rental-tile --pod
$ ember generate route rental --pod
```

Pods are still an experimental feature in Ember, and their format may change. Within the next few releases, however, they will stabilize and become the new standard file structure.
