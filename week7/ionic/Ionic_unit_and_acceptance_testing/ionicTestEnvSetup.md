## Ionic Testing environment setup

The following guide assumes that you already have a Ionic application scaffolded and configured. Following the outlined steps, will help you in setting up frameworks for running both unit and acceptance tests.

Testing is challenging and frustrating even for experienced developers. Writing tests and getting them to pass takes patience and experience. You should learn as much as possible about testing and practice as much as you can. Take your time, ask questions of your coaches, fellow peers, seek assistance online, and above all, donâ€™t let a continuously failing test get the better of you. Follow the TDD process (Acceptance-Unit Test Cycle) as much as possible. Also, adding test coverage metrics to your test suite will help you to stay on track. You will overcome it.

In our project folder, we need to install some dependencies.

```shell
$ npm install --save-dev angular2-template-loader html-loader jasmine jasmine-spec-reporter karma karma-chrome-launcher karma-jasmine karma-jasmine-html-reporter karma-sourcemap-loader karma-webpack karma-coverage-istanbul-reporter istanbul-instrumenter-loader null-loader protractor ts-loader ts-node @types/jasmine @types/node ionic-mocks
```

This command adds these modules to the "devDependencies"node of your projectâ€™s package.jsonfile. There are quite a few modules here, but the important modules are karma, jasmine and protractor. Karma is the module which is our testing environment for unit testing. Jasmine is is the unit testing framework. Protractor is the module which is our testing environment for our end-to-end tests.

The rest of the modules, that we installed with the command above, are utilities that allow this configuration to work.

Next thing we need to do is add some scripts to our package.json
```javascript
"test": "karma start ./test-config/karma.conf.js" - This script will open a browser and run continuously until we shut it down in the terminal. In the browser we have to press debug to see a more detailed report.
"test-ci": "karma start ./test-config/karma.conf.js --single-run" - This script runs our test once and show the status in the terminal.
"test-coverage": "karma start ./test-config/karma.conf.js --coverage" - This script will run our tests and add a test coverage report that we can open coverage/index.html with our browser.
"e2e-test": "protractor ./test-config/protractor.conf.js" - This script runs our protractor test (Acceptance tests).
"e2e-update": "webdriver-manager update --standalone false --gecko false" - This script updates our selenium webdriver.
"e2e": "npm run e2e-update && npm run e2e-test" - This script updates and runs our acceptance tests.
```

We use those scripts to run our tests from the terminal by adding them to `npm run`.
f.ex

```shell
$ npm run test
```


Now our file should look like this (the dependencies are excluded for better readability).

```javascript
# package.json

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

### Unit test configuration
Now, we need to create a folder called test-config in our project root folder.

```shell
$ mkdir test-config
```

Next, we need to create configurations files in that folder.

First is the configuration files for Karma, our unit test runner. We will not go over each setting but you should do your own research.
```javascript
# test-config/karma.conf.js

var webpackConfig = require('./webpack.test.js');

module.exports = function(config) {
  var _config = {
    basePath: '../',

    frameworks: ['jasmine'],

    files: [
      {
        pattern: './test-config/karma-test-shim.js',
        watched: true
      },
      {
        pattern: './src/assets/**/*',
        watched: false,
        included: false,
        served: true,
        nocache: false
      }
    ],

    proxies: {
      '/assets/': '/base/src/assets/'
    },

    preprocessors: {
      './test-config/karma-test-shim.js': ['webpack', 'sourcemap']
    },

    webpack: webpackConfig,

    webpackMiddleware: {
      stats: 'errors-only'
    },

    webpackServer: {
      noInfo: true
    },

    browserConsoleLogOptions: {
      level: 'log',
      format: '%b %T: %m',
      terminal: true
    },

    coverageIstanbulReporter: {
      reports: [ 'html', 'lcovonly' ],
      fixWebpackSourcePaths: true
    },

    reporters: config.coverage ? ['kjhtml', 'dots', 'coverage-istanbul'] : ['kjhtml', 'dots'],
    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    browsers: ['Chrome'],
    singleRun: false
  };

  config.set(_config);
};
```

```javascript
# test-config/karma-test-shim.js

Error.stackTraceLimit = Infinity;

require('core-js/es6');
require('core-js/es7/reflect');

require('zone.js/dist/zone');
require('zone.js/dist/long-stack-trace-zone');
require('zone.js/dist/proxy');
require('zone.js/dist/sync-test');
require('zone.js/dist/jasmine-patch');
require('zone.js/dist/async-test');
require('zone.js/dist/fake-async-test');

var appContext = require.context('../src', true, /\.spec\.ts/);

appContext.keys().forEach(appContext);

var testing = require('@angular/core/testing');
var browser = require('@angular/platform-browser-dynamic/testing');

testing.TestBed.initTestEnvironment(browser.BrowserDynamicTestingModule, browser.platformBrowserDynamicTesting());
```

Next, we need to create the configuration file for Protractor, our acceptance testing framework.

```javascript
# test-config/protractor.conf.js

const { SpecReporter } = require('jasmine-spec-reporter');

exports.config = {
  allScriptsTimeout: 11000,
  specs: [
    '../e2e/**/*.e2e-spec.ts'
  ],
  capabilities: {
    'browserName': 'chrome'
  },
  directConnect: true,
  baseUrl: 'http://localhost:8100/',
  framework: 'jasmine',
  jasmineNodeOpts: {
    showColors: true,
    defaultTimeoutInterval: 30000,
    print: function() {}
  },
  onPrepare() {
    require('ts-node').register({
      project: 'e2e/tsconfig.e2e.json'
    });
    jasmine.getEnv().addReporter(new SpecReporter({ spec: { displayStacktrace: true } }));
  }
};
```

And our configuration file for Webpack. Follow this link https://webpack.js.org/concepts/ to get a better understanding of webpack.

```javascript
# test-config/webpack.test.js

var webpack = require('webpack');
var path = require('path');

module.exports = {
  devtool: 'inline-source-map',
  resolve: {
    extensions: ['.ts', '.js']
  },
  module: {
    rules: [{
      test: /\.ts$/,
      loaders: [{
        loader: 'ts-loader'
      }, 'angular2-template-loader']
    },
    {
      test: /.+\.ts$/,
      exclude: /(index.ts|mocks.ts|\.spec\.ts$)/,
      loader: 'istanbul-instrumenter-loader',
      enforce: 'post',
      query: {
        esModules: true
      }
    },
    {
      test: /\.html$/,
      loader: 'html-loader?attrs=false'
    },
    {
      test: /\.(png|jpe?g|gif|svg|woff|woff2|ttf|eot|ico)$/,
      loader: 'null-loader'
    }
  ]
  },
  plugins: [
    new webpack.ContextReplacementPlugin(
      /(ionic-angular)|(angular(\\|\/)core(\\|\/)@angular)/,
      root('./src'),
      {}
    )
  ]
};

function root(localPath) {
  return path.resolve(__dirname, localPath);
}
```

Some of the Karma and Jasmine dependencies dont work very well together so we need to set them to specific versions. We need to modify` package.json` devDependencies part change the following to these versions.

```javascript
"ts-loader": "^3.0.3",
"karma-jasmine-html-reporter": "^0.2.2",
"karma-jasmine": "^1.1.0",
"jasmine": "^2.5.3",
```
And we need to install those dependencies.
```shell
$ npm install
```

### Acceptance test configuration
First we need to create a folder `e2e` in our project root folder.

```shell
$ mkdir e2e
```

Next we need to create a configuration file `tsconfig.e2e.json`. for `Typescript` in the testing environment.

```javascript
# e2e/tsconfig.e2e.json

{
  "extends": "../tsconfig.json",
  "compilerOptions": {
    "outDir": "../out-tsc/e2e",
    "baseUrl": "./",
    "module": "commonjs",
    "target": "es5",
    "typeRoots": [
      "../node_modules/@types"
    ]
  }
}
```
Next, we create a page object. A class with functions that return elements like buttons, texts or whatever you need for the test.

```javascript
# e2e/app.po.ts

import { browser, by, element } from 'protractor';

export class Page {

  navigateTo(destination) {
    return browser.get(destination);
  }

  getTitle() {
    return browser.getTitle();
  }

  getPageOneTitleText() {
    return element(by.tagName('page-one')).element(by.tagName('ion-title')).getText();
  }
}
```

Last file we need to create is `app.e2e-spec.ts` It's in this file we write our acceptance tests.

```javascript
# e2e/app.e2e-spec.ts

import { Page } from './app.po';

describe('App', () => {
  let page: Page;

  beforeEach(() => {
    page = new Page();
  });

  describe('default screen', () => {
    beforeEach(() => {
      page.navigateTo('/');
    });

     it('should have a title saying Ionic App', () => {
      page.getTitle().then(title => {
        expect(title).toEqual('Ionic App');
      });
    });
  })
});
```

###Running the e2e tests

When running the e2e tests we first have to fire up ionic serve --lab in another terminal tab. To open a new tab in your terminal select your terminal and press `Command+t`, on a linux you press `control-shift-t`.

When you have Ionic server running, you can run `npm run e2e` to run your acceptance tests.