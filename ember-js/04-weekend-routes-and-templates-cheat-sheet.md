Making a Route:

```
$ember g route [name-of-route]
``` 

Generator creates three new files: 

1. route handler ( app/routes/name-of-route.js ) 
2. one for the template the route handler will render ( app/templates/name-of-route.hbs ) 
3. a test file ( tests/unit/routes/name-of-route-test.js ) 
4… the router file is also touched ( app/router.js ) 

Index route is special: doesn’t require an entry in the router’s mapping:

```
$ember g route index 
```

{{link-to}} helper:

To create a link: 
`{{#link-to ‘index’}}Home{{/link-to}}`
