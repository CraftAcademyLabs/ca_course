## Accaptence testing

We are going to use jest and puppeteer to accetptance test this application. Start with adding the packages we need:

`npm i -D cross-env jest-dev-server jest-puppeteer puppeteer`

Now we need to configure this properly. In the root folder we will add to files, `jest-puppeteer.config.js` and `jest.config.js`.

Add this code there:

```js
// jest-puppeteer.config.js
const port = process.env.TEST_SERVER_PORT
    ? Number(process.env.TEST_SERVER_PORT)
    : 3000

process.env.TEST_SERVER_PORT = port

module.exports = {
    launch: {
        headless: process.env.CI === 'true',
    },
    browserContext: process.env.INCOGNITO ? 'incognito' : 'default',
    server: {
        command: `cross-env PORT=${port} react-scripts start`,
        port,
        launchTimeout: 4000,
    },
} 
```

```js
// jest.config.js
module.exports = {
    verbose: true,
    preset: "jest-puppeteer",
    testRegex: ".feature\\.js$"
}; 
```

You also have to add a script to the `package.json` to run these e2e tests. 

```json
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "e2e": "jest -c jest.config.js"
  },
```

Now it is time that we write our first test, wich is going to be the scenario of calculating the BMI. The test that you are now going to see has the imperial method already implmented. It could be that you have also implemented the imperial method but in a different way because we gave you free hands with that feature. This is just our example that works with our implementation. We encourage you to update this test so it works for your implementation.

Run:

`touch src/BMICalculator.feature.js`


This is the acceptance test for our BMI calculator:

```js
describe('BMI Converter', () => {
    beforeAll(async () => {
        await page.goto('http://localhost:3000');
    });

    beforeEach(async () => {
        await page.reload();
    });

    it('should display "BMI Converter" text on page', async () => {
        await expect(page).toMatch('BMI Converter');
    });

    describe('Metric method', async () => {
        beforeEach( async () => {
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

The important part that you need to add is the `beforeEach` and `beforeAll` blocks. This  has nothing do with what implementation you have. The `beforeAll` makes sure that the test browser goes to the correct URL to run the tests and the `beforeEach` resets the page (and the input fields) after every test. The testing of the actual method may change depending on the implementation that you have. As you can see it is pretty straight forward, we select the method from a selector, fll in the inputs with the relevant information and then expect what response is going to be shown on the page. If you need to press a button or interact with the page in some different way then what we show you above, you can go and find that in the [puppeteer documentation](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md).




















































