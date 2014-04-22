Controller 'as' Syntax
----------------------

Angular has a litte know template syntax called 'Controller as' that allows you have refernse the controller object itself, and not just the scope.

This technique is powerful as it allows you to seperate concerns over the Scope. Scope is a power concept for data and communications, but not ideal 
for exposting methods. 'Controller as' allows you to expose methods using `this` inside of a Controller instead of declaring them on the `$scope`.

```html
<div ng-controller="UserSettingsController as usersettings">
    <input ng-model="user.username">
    <button ng-click="usersettings.updateName(user.username)">Update Name</button>
</div>
```

```javascript
angular.module('myapp.controllers.usersettings', [])
.controller('UserSettingsController', ['$scope', function($scope){
    this.updateName = function(username) {
        // Code here
    };
}]);
```
