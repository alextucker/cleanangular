Basic Controller Layout
-----------------------

Some disciple about how you simply layout the different concerns in a controller will go a long way when
it comes to maintainability and working with a team. Below is an example of how you can organize your code
inside of a controller.

```javascript
angular.module('myapp.controllers.example', [])
.controller('ExampleController', ['$scope', 'UserService', function($scope, UserService){
    // **
    // Initial State

    $scope.example = {
        loading: {
            user: false
        }
    };


    // **
    // Initial Data

    UserService.getUserById(1)
    .then(function(user){
        $scope.user = user;
        $scope.example.loading.user = false;
    });

    //**
    // Controller API (Public)

    this.changeUsername = function(username) {
        // Code Here
    };


    //**
    // Controller API (private)

    this._privateMethod = function(arg1) {
        // Code here
    };
}]);
```
