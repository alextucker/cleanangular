Promise Returning Service Pattern
----------------------------------

Promises are a powerful way of expressing asyc operations. Many Services in Angular that you will write will be hiding async 
actions; using the network calls to the API or even WebWorkers.

```javascript
angular.module('myapp.services.usersettings', [])
.factory('UserSettingsService', ['$q', '$http', function($q, $http){
    return {
        getSettings: function(id) {
            var defer = $q.defer(); // A defer object that you control

            $http.get('some/url')
            .success(function(resp){
                defer.resolve(data);
            })
            .error(function(){
                defer.reject(data);
            });

            return defer.promise; // Return the promise of the defer you control
        }
    }
}]);
```

This pattern becomes very powerful at test time. Instead to mock $http, you can just control a promise

```javascript
describe('MyController', function(){
    // Scope these somewhere accessible to inside beforeEach and it blocks
    var ctrl, scope, userSettingsDefer;

    beforeEach(function(){
        angular.mock.module('myapp.example.controllers.user');

        angular.mock.inject(function($rootScope, $controller, $q){
            userSettingsDefer = $q.defer();
            scope = $rootScope.$new();
            var mock = {
                getSettings: function(id) {
                    return userSettingsDefer.promise;
                }
            };

            ctrl = $controller('UserController', {
                $scope: scope,
                UserSettingsService: mock
            });
        });

    });

    it('should get user settings', function(){
        // This method will call a service, and when resolved
        // munge the data a little and attach it to the scope.
        ctrl.someMethodThatRefreshesSettings();

        // Resolve the promise
        userSettingsDefer.resolve({someSetting: '1234'});

        // DONT FORGET THIS
        // You need to manually trigger the digest so the promise watchers will propogate
        $rootScope.$digest();

        // Now assert that the code listening the promise did what you expected.
        expect(scope.settings.someSetting).to.equal('abc_1234');
    });
});
```
