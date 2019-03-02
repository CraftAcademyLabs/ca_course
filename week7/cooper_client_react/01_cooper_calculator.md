---
title: "Cooper client in React"
subtitle: "Advanced React"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "React, Javascript"
keywords: [React, Javascript]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Cooper client in React

First, we need to create an application. Run this command in the terminal to create a React application using the `create-react-app` generator:


`npx create-react-app cooper_client`

We are going to set up the acceptance testing first (aka feature tests). Like we did in the BMI Calculation project, we are going to use `jest-puppeteer`.

First, we need to add the appropriate packages:
`$ npm i -D jest-dev-server@^3.9.0 jest-puppeteer@^3.9.0 puppeteer`

**Note theat we are locking the versions of `jest-dev-server` and `jest-puppeteer` to version `3.9.0`.** Make sure that the versions of these libraries in the `package.json` file are set to at least these versions (or higher):

```json
  "devDependencies": {
    // ..
    "jest-dev-server": "^3.9.0",
    "jest-puppeteer": "^3.9.0"
  }

```

We need to configure these libraries now. In order to do that, we need to create 2 configurations files. Create them in your projects root folder:

```shell
$ touch jest-puppeteer.config.js
$ touch jest.config.js
```
And add the following settings in each file (see the file name below):

```js
// jest.config.js
module.exports = {
  verbose: true,
  preset: "jest-puppeteer",
  testRegex: ".feature\\.js$"
};  
```

```js
// jest-puppeteer.config.js
module.exports = {
  launch: {
    headless: false,
    slowMo: 10,
    devtools: true,
    timeout: 100000,
    args: [
      '--disable-setuid-sandbox',
      '--no-sandbox',
      '--ignore-certificate-errors',
      "--disable-popup-blocking",
      "--disable-infobars",
      '--disable-web-security'
    ]
  },
  browserContext: 'default',

  server: {
    command: `BROWSER=none npm run start`,
    port: 3001,
    launchTimeout: 4000,
  },
}
```

The next step is to add a script to the `package.json` file to be able to run feature tests. We also want to modify the `start` script so the application runs on `localhost://3001`, the reason for this is that we want to be able to both run the Rails backend and this client at the same time.

```json
"scripts": {
    "start": "PORT=3001 react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "features": "jest -c jest.config.js"
 },
```

There's another bit of setup/configuration we need to do. That is to tell out IDE to silent some errors by exporting some global variables created by the `jest-puppeteer`library. Create a new file in the root folder of your application and call it `.eslintrc.js`. Add the following code to that file: 

```javascript 
module.exports = {
  env: {
    jest: true,
  },
  globals: {
    page: true,
    browser: true,
    context: true,
    jestPuppeteer: true,
  },
}
```
It is time to add our first test. Create the following folder and feature file:

```
$ mkdir src/__features__
$ touch src/__features__/userCanGetCooperCalculationResult.feature.js
```

In the first test, we will make sure that the user can input his/hers data and get the correct result from the application. Add the following configuration and features: 

```js
describe('Cooper Client', async () => {

  beforeAll(async () => {
    await page.goto('http://localhost:3001');
  });

  beforeEach(async () => {
    await page.reload();
  })
  
  describe('calculates successfully', async () => {
    beforeEach(async () => {
      await page.type('input[id="distance"]', '1000')
      await page.select('select[id="gender"]', 'female')
      await page.type('input[id="age"]', '23')
    })

    it('displays age', async () => {
      await expect(page).toMatch('23 y/o')
    })

    it('displays gender', async () => {
      await expect(page).toMatch('female')
    })

    it('displays distance', async () => {
      await expect(page).toMatch('running 1000 meters')
    })

    it('displays result', async () => {
      await expect(page).toMatch('Result: Poor')
    })
  })
})
```

This is a pretty straight forward test. The user fills in the distance, selects their gender and types in their age. When all of these inputs have values we should get a response with their result.

To execute the feature tests you need to run the script we added in the `package.json` earlier.
`npm run features`

***"++Remember to commit often++"***

The `create-react-app` scaffolds a lot of code for us. We don't need parts of it. Let's start with cleaning up `App.js` file that contains the App component. When we are done, we want to add our own code to it and define the inputs and selectors that we need to make our feature test go green.

We can safely remove/delete the following files: 
* `App.css`
* `App.test.js`

Change the imports and the `render` function of the App component to look like this:

```js
import React, { Component } from 'react';

class App extends Component {
 render() {
    return (
      <div>
        <div>
          <label>Distance</label>
          <input id="distance"></input>
        </div>

        <select id="gender">
          <option value="female">Female</option>
          <option value="male">Male</option>
        </select>

        <div>
          <label>Age</label>
          <input id="age"></input>
        </div>
      </div>
    );
  }
}

export default App;
```

The test should now be able to find all the input fields that we neet to fill in. At this stage all the steps in the `beforeEach` block in the feature test should run without any problems.

The response should look something like this in the terminal.

![text](React_cooper_client_test_response.png)

Now it is time for us to display the results of calculations based on the data the user has submitted. We will move away from the acceptance tests for a moment, and focus on testing our components. Especially the component we will create for displaying the results. 

We will store our component tests in a separate folder (called `__tests__`) and use a different set of testing frameworks. 

Let's go through the setup by creating folders and adding dependecies. Execute the following commands in your terminal:

```shell
$ npm i -D enzyme enzyme-adapter-react-16 react-test-renderer
$ touch src/setupTests.js
$ mkdir src/__tests__
$ touch src/__tests__/displayCooperResult.test.js
```

Add this to the files we created above:

```js
// setupTests.js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() }); 
```

```js
// displayCooperResult.test.js
import React from 'react';
import { shallow } from 'enzyme';

import DisplayCooperResult from '../Components/DisplayCooperResult';

describe('<DisplatCooperResult />', () => {
  it('evaluates the correct result for female/poor', () => {
    const describedComponent = shallow(<DisplayCooperResult distance="1000" gender="female" age="23"/>);
    const response = <p>Result: Poor</p>
    expect(describedComponent.contains(response)).toEqual(true)
  })

  it('evaluates the correct result for female/average', () => {
    const describedComponent = shallow(<DisplayCooperResult distance="2000" gender="female" age="23"/>);
    const response = <p>Result: Average</p>
    expect(describedComponent.contains(response)).toEqual(true)
  })
})
```

After doing the BMI Calculator we know how to send in props to a child component. We send in `distance`, `gender` and `age`. Then we expect to get a result that the component renders.

If you run the component tests now (using the `npm run test` command in the terminal), you will see that the test complains that it can't find the `DisplayCooperResult` module. Let's create it:

`$ mkdir src/Components`

`$ touch src/Components/DisplayCooperResult.js`

To start with, we will add a stateful/class component with a `render` method. We will expand it with more functionality as we move ahead.

The component should look like this:

```js
import React, { Component } from 'react';

class DisplayCooperResult extends Component {

  render() {
    return (
      <div>
      </div>
    )
  }
}

export default DisplayCooperResult
```

When you run the component tests now you should get another error. The new error states that our expectation/assertion is returning `false`. The component is not returning what it is supposed to when it gets the values from the input fields. It is now time to add some logic to our `DisplayCooperResult` component. We will store that logic in a separate file in a folder we will create for that very purpose. Please look carefully at the imports below, to figure out where that is. 

```js
import React, { Component } from 'react';
import CooperCalculator from '../Modules/CooperCalculator';

class DisplayCooperResult extends Component {

  calculate() {
    return CooperCalculator(this.props.distance, this.props.gender, this.props.age);
  }

  render() {
    let results
    if (this.props.age !== '' && this.props.distance !== '') {
      results =
        <div>
          <p>{this.props.age} y/o {this.props.gender} running {this.props.distance} meters.</p> 
          <p>Result: {this.calculate()}</p>
        </div>
    }
    return (
      <div>
        {results}
      </div>
    )
  }
}

export default DisplayCooperResult
```

If the props the component receives are not empty, it will set the results variable we declared above the `if` statement (we called it `results`) with some HTML code. In the HTML code we display the `age`, `gender` and `distance`, but we also call on `this.calculate()` function. That function is defined above the `render` method and makes use of the cooper normative table logic (see the call to the `copperCalculator()`). 


However, when you run your test, you will see the following error message (or something similiar):

```bash
 FAIL  src/__tests__/displayCooperResult.test.js
  ● Test suite failed to run

    Cannot find module '../Modules/CooperCalculator' from 'DisplayCooperResult.js'

      12 |     if (this.props.age !== '' && this.props.distance !== '') {
      13 |       results =
    > 14 |         <div>
         |                                            ^
      15 |           <p>{this.props.age} y/o {this.props.gender} running {this.props.distance} meters.</p> 
      16 |           <p>Result: {this.calculate()}</p>
      17 |         </div>

      at Resolver.resolveModule (node_modules/jest-resolve/build/index.js:221:17)
      at Object.<anonymous> (src/Components/DisplayCooperResult.js:14:48)

Test Suites: 1 failed, 1 total
Tests:       0 total
Snapshots:   0 total
Time:        0.099s

```

We are telling the componet to import the logic from a module to which we extracted the actual calculation, but we have not created that file yet. Let's go ahead and do that:

`$ mkdir src/Modules`

`$ touch src/Modules/CooperCalculator.js`

Add this code in there:

```js
export const CooperCalculator = (distance, gender, age) => {

  const ratings = [
    'Excellent',
    'Above average',
    'Average',
    'Below average',
    'Poor'
  ];

  const cooperTable = {
    female: {
      '13-14': ['>2000', '1900-1999', '1600-1899', '1500-1599', '<1500'],
      '15-16': ['>2100', '2000-2099', '1700-1999', '1600-1699', '<1600'],
      '17-19': ['>2300', '2100-2299', '1800-2099', '1700-1799', '<1700'],
      '20-29': ['>2700', '2200-2699', '1800-2199', '1500-1799', '<1500'],
      '30-39': ['>2500', '2000-2499', '1700-1999', '1400-1699', '<1400'],
      '40-49': ['>2300', '1900-2299', '1500-1899', '1200-1499', '<1200'],
      '50+': ['>2200', '1700-2199', '1400-1699', '1100-1399', '<1100']
    },
    male: {
      '13-14': ['>2700', '2400-2699', '2200-2399', '2100-2199', '<2100'],
      '15-16': ['>2800', '2500-2799', '2300-2499', '2200-2299', '<2200'],
      '17-19': ['>3000', '2700-2999', '2500-2699', '2300-2499', '<2300'],
      '20-29': ['>2800', '2400-2799', '2200-2399', '1600-2199', '<1600'],
      '30-39': ['>2700', '2300-2699', '1900-2299', '1500-1999', '<1500'],
      '40-49': ['>2500', '2100-2499', '1700-2099', '1400-1699', '<1400'],
      '50+': ['>2400', '2000-2399', '1600-1999', '1300-1599', '<1300']
    }
  };

  const ageRangeTable = age => {
    switch (true) {
      case age >= 13 && age <= 14:
        return '13-14';
      case age >= 15 && age <= 16:
        return '15-16';
      case age >= 17 && age <= 19:
        return '17-19';
      case age >= 20 && age <= 29:
        return '20-29';
      case age >= 30 && age <= 39:
        return '30-39';
      case age >= 40 && age <= 49:
        return '40-49';
      case age >= 50:
        return '50+';
      default:
        return 'invalid range';
    }
  }

  const ageRange = ageRangeTable(parseInt(age));

  if (ageRange === 'invalid range') {
    return 'Invalid age range';
  }

  const distanceRanges = cooperTable[gender.toLowerCase()][
    ageRange
  ];

  let ratingIndex;

  distanceRanges.forEach((dRange, index) => {
    if (
      (dRange.match(/>\d*/) && distance >= parseInt(dRange.slice(1), 10)) ||
      (dRange.match(/<\d*/) && distance < parseInt(dRange.slice(1), 10))
    ) {
      ratingIndex = index;
    } else {
      const minMax = dRange.split('-');
      const min = parseInt(minMax[0], 10);
      const max = parseInt(minMax[1], 10);

      if (distance >= min && distance <= max) {
        ratingIndex = index;
      }
    }
  });
  return ratings[ratingIndex];
}

export default CooperCalculator;
```

If you run the component test now, both tests should go green. Let's run the feature tests again, everything should go green right. We have a component that displays the correct result.

![text](cooper_challenge_component_test_green.png)

BUT, as you can see when we run the feature tests, nothing has changed. That's because we haven't actually rendered the `DiplayCooperResult` component in the `App` component. Consequently, we haven't passed in any props to the `DisplayCooperResult` component. We haven't even saved what gets written in the input fields. So, the actual `DiplayCooperResult` component works, but not with the rest of the application. This is the reason they are called component tests.

Let's add some code to the `App` component.

```js
import React, { Component } from 'react';
import DisplayCooperResult from './Components/DisplayCooperResult';

class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      distance: '',
      gender: 'female',
      age: ''
    }
  }

  onChange(event) {
    this.setState({
      [event.target.id]: event.target.value
    })
  }

 render() {
    return (
      <div>
        <div>
          <label>Distance</label>
          <input id="distance" onChange={this.onChange.bind(this)}></input>
        </div>

        <select id="gender" onChange={this.onChange.bind(this)}>
          <option value="female">Female</option>
          <option value="male">Male</option>
        </select>

        <div>
          <label>Age</label>
          <input id="age" onChange={this.onChange.bind(this)}></input>
        </div>

        <DisplayCooperResult
          distance={this.state.distance}
          gender={this.state.gender}
          age={this.state.age}
        />
      </div>
    );
  }
}

export default App;
```

If you run the test now everything should go green!

Now we need to clean up the `App` component, we dont want to have the input fields there. The `App` component should be as clean as possible, that is why we will extract the input fields to their own component.

`$ touch src/Components/InputFields.js`

So all the input fields we have in the `App` component will be placed here instead:

```js
import React from 'react';

const InputFields = (props) => {
  return (
    <>
      <label>Distance</label>
      <input id="distance" onChange={props.inputChangeHandler}></input>

      <select id="gender" onChange={props.inputChangeHandler}>
        <option value="female">Female</option>
        <option value="male">Male</option>
      </select>

      <label>Age</label>
      <input id="age" onChange={props.inputChangeHandler}></input>
    </>
  )
}

export default InputFields;
```

We need to import this new component into the `App` component before we can use it. 
```js
import InputFields from './Components/InputFields';
```
Then we need to render the `InputFields` component and pass in the onChange method as a prop and bind it. So everytime the value of the input fields change the correlating state changes as well in the `App` component.

```js
// App.js
render() {
  return (
    <div>
      <InputFields 
        inputChangeHandler={this.onChange.bind(this)}
      />
      {/* the rest of the render method... */}
    </div>
  )
}
```
You need to run the tests again to make sure that they still go green!
