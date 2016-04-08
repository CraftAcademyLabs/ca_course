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






