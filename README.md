angular-promise-tracker
=======================

[![Build Status](https://travis-ci.org/ajoslin/angular-promise-tracker.png)](https://travis-ci.org/ajoslin/angular-promise-tracker)

Small, feature filled library used to easily add spinners or general promise/request tracking to your angular app.

* [Quick Start](#quick-start)
* [API Documentation](#api-documentation)
* [Changes](https://github.com/ajoslin/angular-promise-tracker/tree/master/CHANGELOG.md)
* [License](#license)

## Quick Start

The basic idea: each time we add one or more promises to an instance of a `promiseTracker`, that instance's `active()` method will return true until all added promises are resolved. A common use case is showing some sort of loading spinner while some http requests are loading.

[Play with this example on plunkr](http://plnkr.co/edit/PrO2ou9b1uANbeGoX6eB?p=preview)

```sh
$ bower install angular-promise-tracker
```
```html
<body ng-app="myApp" ng-controller="MainCtrl">
  <div class="my-super-awesome-loading-box" ng-show="loadingTracker.active()">
    Loading...
  </div>
  <button ng-click="fetchSomething()">Fetch Something</button>
  <button ng-click="delaySomething()">Delay Something</button>

  <script src="angular.js"></script>
  <script src="angular-promise-tracker.js"></script>
</body>
```
```js
angular.module('myApp', ['ajoslin.promise-tracker'])
.controller('MainCtrl', function($scope, $http, $timeout, promiseTracker) {
  //Create / get our tracker with unique ID
  $scope.loadingTracker = promiseTracker.register('loadingTracker');

  //use `tracker:` shortcut in $http config to link our http promise to a tracker
  $scope.fetchSomething = function(id) {
    return $http.get('/something', {
      tracker: 'loadingTracker'
    }).then(function(response) {
      alert('Fetched something! ' + response.data);
    });
  };

  //use `addPromise` to add any old promise to our tracker
  $scope.delaySomething = function() {
    var promise = $timeout(function() {
      alert('Delayed something!');
    }, 1000);
    $scope.loadingTracker.addPromise(promise);
  };
});
```

## API Documentation

### Service `promiseTracker`

* **`tracker` promiseTracker.register(trackerId[, options])**

  Registers a new promiseTracker with the given identifier.

  Options can be given as an object, with the following allowed values:

  - `activationDelay` `{Number}` - Number of milliseconds that an added promise needs to be pending before this tracker is active.
      * Usage example: You have some http calls that sometimes return too quickly for a loading spinner to look good. You only want to show the tracker if a promise is pending for over 500ms. You put `{activationDelay: 500}` in options.
  - `minDuration` `{Number}` - Minimum number of milliseconds that a tracker will stay active.
      * Usage example: You want a loading spinner to always show up for at least 750ms. You put `{minDuration: 750}` in options.
  - `maxDuration` `{Number}` - Maximum number of milliseconds that a tracker will stay active.
      * Usage example: Your http request takes over ten seconds to come back.  You don't want to display  a loading spinner that long; only for two seconds.  You put `{maxDuration: 2000}` in options.

* **`void` promiseTracker.deregister(trackerId)**

  Deregisters a tracker `register`ed with the given identifier.

* **`tracker` promiseTracker(trackerId)**

  Returns a tracker with `register`ed with the given id.

### **`$http` Sugar**

  * **Any $http call's `config` parameter can have a `tracker` field. Examples:**

  ```js
  //Add $http promise to tracker with id 'myTracker'
  $http('/banana', { tracker: 'myTracker' })
  ```
  ```js
  //Add $http promise to both 'tracker1' and 'tracker2'
  $http.post('/elephant', {some: 'data'}, { tracker: ['tracker1', 'tracker2'] })
  ```

### Instantiated promiseTracker

`var tracker = promiseTracker('myId');`

* **`boolean` tracker.active()**

  Returns whether this tracker is currently active. That is, whether any of the promises added to/created by this tracker are still pending, or the `activationDelay` has not been met yet.

* **`void` tracker.addPromise(promise)**

  Add any arbitrary promise to tracker. `tracker.active()` will be true until `promise` is resolved or rejected.

  - `promise` `{object}` - Promise to add

  Usage Example:

  ```js
  var promise = $timeout(doSomethingCool, 1000);
  console.log(myTracker.active()); // => true
  //1000 milliseconds later...
  console.log(myTracker.active()); // => false
  ```

* **`promise` tracker.createPromise()**

  Creates and returns a new deferred object that is tracked by our promiseTracker.

  Usage Example:

  ```js
  var deferred = myTracker.createPromise()
  console.log(myTracker.active()) // => true
  //later...
  deferred.resolve();
  console.log(myTracker.active()) // => false
  }
  ```

* **`void` tracker.cancel()**

  Causes a tracker to immediately become inactive and stop tracking all current promises.

## Development

* Install karma & grunt with `npm install -g karma grunt-cli` to build & test
* Install local dependencies with `bower install && npm install`
* Run `grunt` to lint, test, build the code, and build the docs site
* Run `grunt dev` to watch and re-test on changes

#### New Versions

## <a id="license"></a>License

> <a rel="license" href="http://creativecommons.org/publicdomain/mark/1.0/"> <img src="http://i.creativecommons.org/p/mark/1.0/80x15.png" style="border-style: none;" alt="Public Domain Mark" /> </a> <span property="dct:title">angular-promise-tracker</span> by <a href="http://ajoslin.com" rel="dct:creator"><span property="dct:title">Andy Joslin</span></a> is free of known copyright restrictions.
