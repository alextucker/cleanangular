Basic Controller Layout
-----------------------

```
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
