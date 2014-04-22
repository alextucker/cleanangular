Information Sharing
-------------------

It is common that you want to share information between controllers.

### What Not To Do

Rely on Parent Scope

Repeat state using services

Share state using service internals

### What To Do

Watch Parent scope for shared data and children create their own independent scopes. Communicate changes via events.
How is this different than a child scope relying on a parent scope? The child is not entirely relyant on the parent 
as it does in the create its own scope. The real problems with using the Parent scope directly is that you can run
into race conditions and side effects very easily. This technique can be refined to use events instead of a `$watch`
if you need more fine grain control or if performance becomes an issue.

### Mutation

When a child scope makes changes to data, it will not directly change the data on the Parent scope. Instead it will
communicate its changes via an event. Events will act as the contract for mutating data in your application.

```javascript
angular.module('myapp.controllers.parent', [])
.controller('ParentController', ['$scope', function($scope){
    $scope.parentData = {
        nestedData: {
            x: true,
            y: 'Foo',
            z: 1234
        },

        nestedCollection: [
            // Collection data
        ]
    };

    $scope.$on('nestedData:change', function(event, data){
        // Persist changes somehow

        // Update parent scope that will communicate changes to children.
        $scope.parentData.nestedData = data;
    });
}]);

angular.module('myapp.controllers.child', [])
.controller('ChildController', ['$scope', function($scope){
    $scope.$watch('parentData.nestedData', function(newValue, oldValue, scope){
        if(newValue === oldValue) return; // Short circuit when you dont need to do anything

        // Child scope creates its own scope derived from the parent's scope.
        // This new child scope is what can be refernced in templates and
        // mutated by the child controller.
        $scope.childData = newValue;
    });

    this.update = function(){
        $scope.emit('nestedData:change', $scope.childData);
    };
}]);
```
