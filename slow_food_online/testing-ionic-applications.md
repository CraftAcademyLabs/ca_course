## Ionic Testing environment setup

In our project folder we need to install some dependencies.

```
npm install --save-dev angular2-template-loader html-loader jasmine jasmine-spec-reporter karma karma-chrome-launcher karma-jasmine karma-jasmine-html-reporter karma-sourcemap-loader karma-webpack karma-coverage-istanbul-reporter istanbul-instrumenter-loader null-loader protractor ts-loader ts-node @types/jasmine @types/node
```

This command adds these modules to the `"devDependencies"`node of your project’s `package.json`file. There are quite a few modules here, but the important modules are`karma`, `jasmine`and`protractor`.  Karma is the  module which is our testing environment for unit testing. Jasmine is the module which is the unit testing framework. Protractor is the module which is our testing environment for our end-to-end tests. The rest of the modules are utilities that allow this configuration to work.

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

### Unit test configuration

Now we need to create a folder called `test-config` in our project root folder.

`$ mkdir test-config`

Next we need to create configurations files.

First is the configuration files for Karma, our unit test runner.

!FILENAME  test-config/karma.conf.js

```js
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

!FILENAME test-config/karma-test-shim.js

```js
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

Next we need to create the configuration file for Protractor, our acceptance testing framework.

!FILENAME test-config/protractor.conf.js

```js
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

Now we create `mocks-ionic.ts`. Ionic Mocks are designed to be used as placeholders during testing for the actual Ionic Native modules.

!FILENAME mocks-ionic.ts

```js
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';

export class PlatformMock {
  public ready(): Promise<string> {
    return new Promise((resolve) => {
      resolve('READY');
    });
  }

  public getQueryParam() {
    return true;
  }

  public registerBackButtonAction(fn: Function, priority?: number): Function {
    return (() => true);
  }

  public hasFocus(ele: HTMLElement): boolean {
    return true;
  }

  public doc(): HTMLDocument {
    return document;
  }

  public is(): boolean {
    return true;
  }

  public getElementComputedStyle(container: any): any {
    return {
      paddingLeft: '10',
      paddingTop: '10',
      paddingRight: '10',
      paddingBottom: '10',
    };
  }

  public onResize(callback: any) {
    return callback;
  }

  public registerListener(ele: any, eventName: string, callback: any): Function {
    return (() => true);
  }

  public win(): Window {
    return window;
  }

  public raf(callback: any): number {
    return 1;
  }

  public timeout(callback: any, timer: number): any {
    return setTimeout(callback, timer);
  }

  public cancelTimeout(id: any) {
    // do nothing
  }

  public getActiveElement(): any {
    return document['activeElement'];
  }
}

export class StatusBarMock extends StatusBar {
  styleDefault() {
    return;
  }
}

export class SplashScreenMock extends SplashScreen {
  hide() {
    return;
  }
}

export class NavMock {

  public pop(): any {
    return new Promise(function(resolve: Function): void {
      resolve();
    });
  }

  public push(): any {
    return new Promise(function(resolve: Function): void {
      resolve();
    });
  }

  public getActive(): any {
    return {
      'instance': {
        'model': 'something',
      },
    };
  }

  public setRoot(): any {
    return true;
  }

  public registerChildNav(nav: any): void {
    return ;
  }

}

export class DeepLinkerMock {

}
```

And our configuration file for Webpack. Follow this link `https://webpack.js.org/concepts/` to get a better understanding of webpack.

!FILENAME test-config/webpack.test.js

```js
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

### Acceptance test configuration

First we need to create a folder `e2e` in our project root folder.

`$ mkdir e2e`

First we need to create a configuration file `tsconfig.e2e.json.`for Typescript in the testing environment.

!FILENAME e2e/tsconfig.e2e.json

```js
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

Next we create a page object. A class with functions that return elements like buttons, texts or whatever you need for the test.

!FILENAME e2e/app.po.ts

```js
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

Last file we need to create is `app.e2e-spec.ts` Its in this file we write our acceptance tests.

!FILENAME e2e/app.e2e-spec.ts

```js
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

    it('should have a title saying Page One', () => {
      page.getPageOneTitleText().then(title => {
        expect(title).toEqual('Page One');
      });
    });
  })
});
```

#### Running the e2e tests

When running the e2e tests we first have to fire up `ionic serve --lab` in another terminal tab. To open a new tab in your terminal select your terminal and on a mac you press `Command+t`, on a linux you press `control-shift-t`.

When you have ionic server running you can run `npm run e2e` to run your acceptance tests.

