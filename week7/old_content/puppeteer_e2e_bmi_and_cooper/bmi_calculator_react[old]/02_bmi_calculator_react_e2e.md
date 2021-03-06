## Acceptance testing

We are going to use [Jest](https://jestjs.io/) together with [Puppeteer](https://developers.google.com/web/tools/puppeteer/) to write our acceptance test for this application. 

We will start by adding some of the npm packages we need and configure the setup in order to be able to run our tests properly. In the root folder, we will add two files, `jest-puppeteer.config.js` and `jest.config.js`.

## Execution

```bash
$ npm i -D jest-dev-server@^3.9.0 jest-puppeteer@^3.9.0 puppeteer
$ touch jest-puppeteer.config.js
$ touch jest.config.js
```

**Note that we are locking the versions of `jest-dev-server` and `jest-puppeteer` to version `3.9.0`.** Make sure that the versions of these libraries in the `package.json` file are set to at least these versions (or higher):

```json
  "devDependencies": {
    // ..
    "jest-dev-server": "^3.9.0",
    "jest-puppeteer": "^3.9.0"
  }
```

Open up the configuration files and add the following settings:

```js
// jest.config.js
module.exports = {
    verbose: true,
    preset: "jest-puppeteer",
    testRegex: ".feature\\.js$"
}; 
```
This will tell Jest that we want to use Puppeteer on on all test files that end with `.feature.js`

```js
// jest-puppeteer.config.js
module.exports = {
    launch: {
        headless: false,
        slowMo: 10,
        devtools: true,
        timeout: 100000,
        args: ['--disable-setuid-sandbox',
            '--no-sandbox',
            '--ignore-certificate-errors',
            "--disable-popup-blocking",
            "--disable-infobars",
            '--disable-web-security']
    },
    browserContext: 'default',

    server: {
        command: `BROWSER=none npm run start`,
        port: 3000,
        launchTimeout: 4000,
    },
    
} 
```

This will tell Jest that we want to start the application on port 3000 and run a visible browser (unless we are in the CI environment).



You also have to add a script to the `package.json` to run these e2e tests. 

```json
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "features": "jest -c jest.config.js"
  },
```
## Your first tests

Now it is time that we write our first test, which is going to be the scenario of calculating the BMI. The test that you are now going to see has the imperial method already implemented. It could be that you have also implemented the imperial method but in a different way because we gave you free hands with that feature. 

This is just our example that works with our implementation. We encourage you to update this test so it works for your implementation.

Run:

```bash
$ mkdir src/__features__
$ touch src/__features__/userCanCalculateTheirBMI.feature.js
```


Read through the code (and comments) below and add it to the feature file you just created:

```js
describe('BMI Converter', () => {
    beforeAll(async () => {
        // visit the application running on the Jest Development Server
        await page.goto('http://localhost:3000');
    });

    beforeEach(async () => {
        // Reload the application between tests to reset state
        await page.reload();
    });

    it('should display "BMI Converter" text on page', async () => {
        await expect(page).toMatch('BMI Converter');
    });

    describe('Metric method', async () => {
        beforeEach( async () => {
            // This before block will be executed prior to each test in this describe block
            await page.select('select[id="method"]', 'metric')
            await page.type('input[name="weight"]', '95')
            await page.type('input[name="height"]', '186')
        })

        it('displays assesment', async () => {   
            await expect(page).toMatch('You are Overweight')
        })

        it('displays BMI value', async () => {   
            await expect(page).toMatch('BMI of 27.46')
        })
    })

    describe('Imperial method', async () => {
        beforeEach( async () => {
            // This before block will be executed prior to each test in this describe block
            await page.select('select[id="method"]', 'imperial')
            await page.type('input[name="weight"]', '200')
            await page.type('input[name="height"]', '73')
        })

        it('displays assesment', async () => {   
            await expect(page).toMatch('You are Overweight')
        })

        it('displays BMI value', async () => {   
            await expect(page).toMatch('BMI of 26.38')
        })
    })
}); 
```

The important part that you need to understand is what is happening in the `beforeEach` and `beforeAll` blocks. The `beforeAll` makes sure that the test browser goes to the correct URL to run the tests. The `beforeEach` resets the page (and the input fields) after every test. 

The testing of the actual method may change depending on the implementation that you have. As you can see it is pretty straight forward, we select the method from a selector, fill in the inputs with the relevant information and then expect what response is going to be shown on the page. 

If you need to press a button or interact with the page in some different way then what we show you above, you can go and find that in the [Puppeteer documentation](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md).

## Wrap up
Acceptance tests help you in making sure that your application does what it is supposed to do when a user interacts with it. It requires limited knowledge about the implementation itself (how each component works). Acceptance tests are just one of the testing strategies we will use when developing our React applications.
