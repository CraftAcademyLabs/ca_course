# Saving and retrieving data - step 6
At this stage we have a possibility to interact with the back and login to the mobile application. It is time to set up a mechanism to both save and retrieve our test results.

We have an API endpoint in our beck-end that can take a POST and a GET request, right? Let's build a service that will give us a way to access it. 

We will use [`ngResource`](https://docs.angularjs.org/api/ngResource) to access out API and perform out requests. 

```
$ bower install angular-resource --save
```

Create a new file in the `www/js` folder and call it `services.js` 
```
$ touch www/js/services.js
```

That's where we will add our Factory.

!FILENAME www/js/services.js

```javascript

```





