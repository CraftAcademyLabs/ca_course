### Semaphore setup

1 Create `serve-static.js` file in the root folder of the Ionic app
2 paste this into `serve-static.js`
  ```js
  var connect = require('connect');
  var serveStatic = require('serve-static');
  connect().use(serveStatic("www")).listen(8100, function () {
    console.log('Server running on 8100...');
  });
  ```
3 in `package.json` add two new scripts
  ```js
    "e2e-semaphore": "ionic-app-scripts build; node serve-static.js& npm run e2e:standalone; kill %1",
    "e2e:standalone": "webdriver-manager update --standalone false --gecko false; protractor ./test-config/protractor.conf.js"
  ```
4 in `package.json` `devDepencies` update `@ionic/app-scripts` to `^3.2.0`
  ```js
  "@ionic/app-scripts": "^3.2.0",
  ```
5 Set the commands on Semaphore to
  setup:
    ```sh
    npm install -g ionic cordova
    npm install connect serve-static 
    npm install
    ```
  Job #1:
    ```
    npm run test-ci
    npm run e2e-semaphore
    ```

Now you can run the build and see if the setup is working correctly

One error you might get is that the chrome version is not update to date and the error looks like this
```
[10:41:27] E/launcher - session not created: Chrome version must be between 70 and 73
  (Driver info: chromedriver=2.45.615279 (12b89733300bd268cff3b78fc76cb8f3a7cc44e5),platform=Linux 4.4.0-116-generic x86_64)
[10:41:27] E/launcher - SessionNotCreatedError: session not created: Chrome version must be between 70 and 73
```
If you have this error add these commands to the top of semaphore setup commands
```
sudo touch /etc/apt/sources.list.d/google-chrome-source.list
install-package --update-new google-chrome-stable
```