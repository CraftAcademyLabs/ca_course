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

And also, make it availible to your app by adding it to the main module.

!FILENAME www/js/app.js
```javascript
angular.module('starter', ['ionic', 'starter.controllers', 'starter.services', 'ng-token-auth', 'ngResource', 'chart.js'])
```






