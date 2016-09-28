**If bootstrap-js is needed, use this instead of `ember install ember-bootstrap`:**

`bower install --save bootstrap`

Add the following 3 lines to ember-cli-build:		

```javascript
app.import('bower_components/bootstrap/dist/css/bootstrap.css');		
app.import('bower_components/bootstrap/dist/css/bootstrap.css.map');		
app.import('bower_components/bootstrap/dist/js/bootstrap.js');
```
