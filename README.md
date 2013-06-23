Backbone.Service
================
[![build status](https://api.travis-ci.org/ViLPy/backbone.service.png)](http://travis-ci.org/ViLPy/backbone.service)

Backbone.Service aims to help with the cases when standard restul API is not an option.

## Install

````javascript
<script src="backbone.service.js"></script>
````

## Usage

You can use backbone.service as a standalone object or extend backbone model or collection.

````javascript

// define server targets / endpoints
var targets = {
  login: {
    path: function(model) {
      return '/' + model.userType
    },
    method: "post",
    data: function(model, options) {
      return model.toJSON();
    }
  },
  check: {
    path: "/check"
    // default method: 'get'
    // default data: function parameter or model.toJSON if parameter empty
  }
  search: "/search"
};

// define Backbone.sync implementations
// they must be matched with Backbone.sync method names: 'read', 'create', 'update', 'delete'
// structure is same as in targets, except functions has only model attribute without options
var sync = {
  delete: {
    path: "/service/remove",
    method: "post",
    data: function(model) {
      return model.toJSON();
    }
  }
}

// standalone service
var service = new Backbone.Service({ url: "http://localhost:5000", targets: targets, sync: sync });

// extend backbone model
var User = Backbone.Model.extend(service);
````
Each target passed to Backbone.Service becomes a method on the model or collection.

`url` parameter can be set for all Service instances via

````javascript

Backbone.Service.url = "http://localhost:8080";
````

User model has now access to new methods: `login`, `check`, `search`.
Each new method takes two arguments: `data` and `options`.

You can use it like this:

````javascript

var user = new User();
user.login({ username: 'bob', password: 'secret' });
````

Each sync re-implementation will be used instead of standard Backbone.sync when calling standard model/collection persistence methods
If some sync method is not implemented, standard Backbone.sync implementation will be used

````javascript

user.destroy(); // will apply custom delete implementation - post method to '/service/remove'
````

Model will be passed to server on each request as additional data, but this can be disabled by setting Backbone.Service.sendModels to false

## Promises / Callbacks

Backbone.service comes with a simple implementation of promises. You can use them like this:

````javascript

user.search(settings).then(function (res) {
  // do something after successful update
}, function (err, res) {
  // do something in case of an error
});
````

Callbacks are still supported. You can pass them as a second argument in your calls:

````javascript
user.search(settings, {
  success: function (res) {
    // do something after successful update
  },
  error: function (err, res) {
    // do something in case of an error
  }
});
````

##Contributors

* [@mkuklis](http://github.com/mkuklis)
* [@scttnlsn](http://github.com/scttnlsn)
* [@vilpy](http://github.com/vilpy)

##License:
<pre>
(The MIT License)

Copyright (c) 2012 Michal Kuklis

</pre>
