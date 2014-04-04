Information Sharing
-------------------

It is common that you want to share information between controllers.

### What Not To Do

Rely on Parent Scope

Repeat state using services

Share state using service internals

### What To Do

Watch Parent scopes for shared data, children create their own independent scopes. Communicate changes via events.
How is this different than a child scope relying on a parent scope? The idea is to watch parent scopes and have
children create their own scopes when the parent changes.

Mutation

When a child scope makes changes to data, it will not directly change the data on the parent scope. Instead it will
communicate its changes via an event. Events will act as the contract for mutating data in your application.

```
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

    $scope.$on('nestedData:change', function(data){
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
