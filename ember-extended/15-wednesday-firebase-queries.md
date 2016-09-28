As we build increasingly complex Ember applications, we'll need increasingly complex ways to return data. Thankfully, Ember Data has some built-in query methods we can utilize for this purpose. 

For instance, say we have a to-do list application built in Ember. The `task` model has several properties: `name`, `details`, `timestamp`, and `done`. `done` is a boolean that refers to whether or not the user has marked the task as completed.

This application contains a page where only  _completed_ tasks are displayed. We can include a query in the model hook of the corresponding route to specify that we'd like to return _only completed_ tasks. This model hook would look like:

```
  model: function() {
    return this.store.query('task', {
      orderBy: 'done',
      equalTo: 'true'
    });
  }

```

Here, we are specifying that we only want Ember Data to return `task` objects whose `done` attributes are equal to `true` - that is, all completed tasks. 

`orderBy` refers to the property we'd like to organize our data by. `equalTo` is an argument that tells Ember Data we'd like to return only records whose attribute listed in `orderBy` are _equal to_ the information we provide `equalTo`. For instance, the following query would only return tasks with `name` attributes equal to "shave the dog": 

```
 model: function() {
    return this.store.query('task', {
      orderBy: 'name',
      equalTo: 'shave the dog'
    });
  }

```

Ember Data also includes the following argument options for queries: 

*  `limitToLast` : This returns only the last specified number of objects. For instance, if our model looked like this it would return _only_ the last 5 completed tasks:

```
model: function() {
    return this.store.query('task', {
      orderBy: 'done',
      equalTo: 'true',
      limitToLast: 5
    });
  }
```

* `limitToFirst`: Returns the _first_ specified number of objects. If we used this argument in our model we would receive the first 5 tasks to be completed:

```
model: function() {
    return this.store.query('task', {
      orderBy: 'done',
      equalTo: 'true',
      limitToFirst: 5
    });
  }
```

* `startAt` : Denotes a minimum value. Let's also assume each of our `task` objects has a `priority` attribute. This `priority` attribute denotes how important the task is. We allow users to give a `task` a `priority` value between 1 and 5. If we constructed our model hook as follows, we could return _only_ tasks with a `priority` rating greater than 3:

```
model: function() {
    return this.store.query('task', {
      orderBy: 'priority',
      startAt: 3
    });
  }
```

*  `endAt`: Denotes a maximum value. Considering this same `priority` attribute, the following query would only return tasks with a `priority` rating less than 3:

```
model: function() {
    return this.store.query('task', {
      orderBy: 'priority',
     endAt: 3
    });
  }
```

These options may be combined, too. For instance, we could query for the first 4 tasks whose priority levels were between 2 and 4:

```
 model: function() {
    return this.store.query('task', {
      orderBy: 'priority',
      limitToFirst: 4,
      startAt: 2,
      endAt: 4,
    });
  }
```

As you know, model hooks are capable of taking parameters. Any parameters passed to the route may be inserted for any of these arguments, similar to the fashion we've been [finding records](https://www.learnhowtoprogram.com/javascript/ember-js/dynamic-routing-find-record) since our first week of Ember. 

Try out some queries of your own in your Ember applications! 

## Additional Resources

For more information, check out the [EmberFire Firebase Documentation](https://www.firebase.com/docs/web/libraries/ember/api.html).
