# Display data - step 7

We will use [Angular Chart](http://jtblin.github.io/angular-chart.js/) to display users historical data. 

```
$ bower install Chart.js --save
$ bower install angular-chart.js --save
```

As always, make sure to include the library in your `intex.html`.

!FILENAME www/index.html
```html
<!-- ionic/angularjs js -->
//...
<script src="lib/Chart.js/Chart.js"></script>
<script src="lib/angular-chart.js/dist/angular-chart.js"></script>

```

And also, make it available to your app by adding it to the main module.

!FILENAME www/js/app.js
```javascript
angular.module('starter', ['ionic', 'starter.controllers', 'starter.services', 'ng-token-auth', 'ngResource', 'chart.js'])
```

Okay, here comes the tricky part. We want to display two charts on our view. One Doughnut Chart and one Radar Chart. The tricky part is that we only want to display labels for values that are actually stored in the collection of historical data. Meaning for instance that if a user has stored several "Average" and "Above Averege" entries, then we should only show those two labels with a value. Nothing else. Same thing goes for both chart types. 

So what we need to do is to go through the `savedDataCollection` and get unique values from `data.message` and store them in an array. This is the responsibility of the following function.

```javascript
function getLabels(collection) {
  var uniqueLabels = [];
  for (i = 0; i < collection.length; i++) {
    if (collection[i].data.message && uniqueLabels.indexOf(collection[i].data.message) === -1) {
      uniqueLabels.push(collection[i].data.message);
    }
  }
  return uniqueLabels;
}
```
We are going to store that array in `$scope.labels`. 

The second thing we need to do is to get the value of how many times each message i present in the collection. For thet we add another function that we use when iteratin over `$scope.labels` and store the results in `$scope.data` 

```javascript
angular.forEach($scope.labels, function(label){
  $scope.data.push(getCount($scope.savedDataCollection, label));
});

function getCount(arr, value){
  var count = 0;
  angular.forEach(arr, function(entry){
    count += entry.data.message == value ? 1 : 0;
  });
  return count;
}
```

All in all, the `DataCtrl` should look something like this.

!FILENAME
```javascript
.controller('DataCtrl', function ($scope, $stateParams) {
  $scope.$on('$ionicView.enter', function () {
    $scope.savedDataCollection = $stateParams.savedDataCollection;
    $scope.labels = getLabels($scope.savedDataCollection);
    $scope.data = [];
    angular.forEach($scope.labels, function(label){
      $scope.data.push(getCount($scope.savedDataCollection, label));
    });
    $scope.radardata = [$scope.data];
  });


  function getLabels(collection) {
    var uniqueLabels = [];
    for (i = 0; i < collection.length; i++) {
      if (collection[i].data.message && uniqueLabels.indexOf(collection[i].data.message) === -1) {
        uniqueLabels.push(collection[i].data.message);
      }
    }
    return uniqueLabels;
  }

  function getCount(arr, value){
    var count = 0;
    angular.forEach(arr, function(entry){
      count += entry.data.message == value ? 1 : 0;
    });
    return count;
  }
})
```

Finally let's turn out attention to the view template. 

We want to add markup for the two charts and clear up the data display. 

!FILENAME
```html
<ion-view title="Historical Data">
  <ion-content>
    <div class="row">
      <div class="col">
        <h5>Saved data for {{currentUser.name || currentUser.email}}</h5>
        <canvas ng-if="savedDataCollection" id="doughnut" class="chart chart-doughnut"
                chart-data="data" chart-labels="labels" legend="true">
        </canvas>

        <canvas ng-if="savedDataCollection" id="radar" class="chart chart-radar"
                chart-data="radardata" chart-labels="labels">
        </canvas>

        <ion-list>
          <ion-item ng-repeat="entry in savedDataCollection " ng-if="savedDataCollection && entry.data.message">
            <span>{{entry.data.message}} - {{entry.created_at | date:'mediumDate'}}</span>
          </ion-item>
        </ion-list>
      </div>
    </div>
  </ion-content>
</ion-view>
```

If you run the application now it should look something like this.

![Cooper Client - Final UI](/images/cooper_client_final.png)









