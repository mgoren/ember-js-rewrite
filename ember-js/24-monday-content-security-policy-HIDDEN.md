Ember has built-in support for [Content Security Policy](http://www.html5rocks.com/en/tutorials/security/content-security-policy/) (CSP).  CSP protects visitors to websites where content can be posted.  Without CSP configurations in place, other sites could potentially load malicious content through [cross-site scripting(XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting). In an effort to make developers mindful of security threats as they are developing, CSP warnings are issued in the console when code may be vulnerable.

To avoid CSP errors by authorizing approved sources, we can add a contentSecurityPolicy property in our _config/environment.js_ file.  It can be placed in the `ENV` variable after the `APP` property.

<div class="filename">config/environment.js</div>
```
...
module.exports = function(environment) {
  var ENV = {
    modulePrefix: 'super-rentals',
    environment: environment,
    baseURL: '/',
    locationType: 'auto',
    EmberENV: {
      FEATURES: {
        // Here you can enable experimental features on an ember canary build
        // e.g. 'with-controller': true
      }
    },

    APP: {
      // Here you can pass flags/options to your application instance
      // when it is created
    },
    contentSecurityPolicy: {
      'default-src': "'none'",
      'script-src': "'self' 'unsafe-eval' https://*.firebaseio.com",
      'font-src': "'self'",
      'connect-src': "'self' wss://*.firebaseio.com",
      'img-src': "'self' *",
      'style-src': "'self' 'unsafe-inline'",
      'frame-src': "'self' https://*.firebaseio.com"
    }
  };
...

```
This example CSP property includes authorization for self access and Firebase access.  It also will allow all (*) image sources.

If we see additional CSP errors, we will need to identify what has not been authorized and add it to our configuration or reconsider how it is used.


