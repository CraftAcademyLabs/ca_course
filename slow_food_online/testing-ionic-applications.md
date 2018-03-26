## Ionic Testing environment setup



In our project folder we need to install some dependencies.

```
npm install --save-dev angular2-template-loader html-loader jasmine jasmine-spec-reporter karma karma-chrome-launcher karma-jasmine karma-jasmine-html-reporter karma-sourcemap-loader karma-webpack karma-coverage-istanbul-reporter istanbul-instrumenter-loader null-loader protractor ts-loader ts-node @types/jasmine @types/node
```

This command adds these modules to the `"devDependencies" `node of your project’s `package.json`file. There are quite a few modules here, but the important modules are`karma`, `jasmine`and`protractor`.  Karma is the  module which is our testing environment for unit testing. Jasmine is the module which is the unit testing framework. Protractor is the module which is our testing environment for our end-to-end tests. The rest of the modules are utilities that allow this configuration to work.

Next thing we need to do is add some scripts to our `package.json.`

`"test": "karma start ./test-config/karma.conf.js"`

With this we can run npm run test and it will open a browser and run continuously until we shut it down in the terminal. In the browser we have to press debug to see a more detailed report. 

`"test-ci": "karma start ./test-config/karma.conf.js --single-run"`

This command `npm run test-ci` runs our test once and show the status in the terminal.

`"test-coverage": "karma start ./test-config/karma.conf.js --coverage"`

This command `npm run test-coverage` will run our tests and add a test coverage report that we can open `coverage/index.html` with our browser.

`"e2e-test": "protractor ./test-config/protractor.conf.js"`

This command `npm run e2e-test` runs our protractor test \(Acceptance tests\).

`"e2e-update": "webdriver-manager update --standalone false --gecko false"`

This command `npm run e2e-update` updates our selenium webdriver.

`"e2e": "npm run e2e-update && npm run e2e-test"`

This command `npm run e2e` both updates and runs our acceptance tests.

Now our file should look like this.

!FILENAME package.json

```
...
"scripts": {
    "clean": "ionic-app-scripts clean",
    "build": "ionic-app-scripts build",
    "lint": "ionic-app-scripts lint",
    "ionic:build": "ionic-app-scripts build",
    "ionic:serve": "ionic-app-scripts serve",
    "test": "karma start ./test-config/karma.conf.js",
    "test-ci": "karma start ./test-config/karma.conf.js --single-run",
    "test-coverage": "karma start ./test-config/karma.conf.js --coverage",
    "e2e": "npm run e2e-update && npm run e2e-test",
    "e2e-test": "protractor ./test-config/protractor.conf.js",
    "e2e-update": "webdriver-manager update --standalone false --gecko false"
  },
  ...
```











