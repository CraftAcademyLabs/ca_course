# Cooper client in React

First, we need to create an application. Run:

`npx create-react-app cooper_client`

We are going to set up the acceptance testing first. Like we did in the BMI Calculation project, we are going to use `jest-puppeteer`.

First, we need to add the packages:
`$ npm i -D jest-dev-server jest-puppeteer puppeteer`

We need to configure it now. Run:
```shell
$ touch jest-puppeteer.config.js
$ touch jest.config.js
```
And add this to them:

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
        args: ['--disable-setuid-sandbox',
            '--no-sandbox',
            '--ignore-certificate-errors',
            "--disable-popup-blocking",
            "--disable-infobars",
            '--disable-web-security']
    },
    browserContext: 'default',

    server: {
        command: `PORT=3001 BROWSER=none npm run start`,
        port: 3001,
        launchTimeout: 4000,
    },
    
} 
```

Now you need to add a script to the `package.json` file to be able to run these tests. We also want to modify the `start` script so the application runs on `localhost://3001`, the reason for this is that we want to be able to both run the rails backend and this client at the same time.

```js
"scripts": {
    "start": "PORT=3001 react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "features": "jest -c jest.config.js"
 },
```
It is time to add our first test now. Run:

`$ mkdir src/__features__`

`$ touch src/__features__/UserCanGetCooperCalculationResult.feature.js`

Add these tests to the new feature file:

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

It is a pretty straight forward test. The user fills in the distance, selects their gender and types in their age. When all of these inputs have values we should get a response with their result.

To execute the feature tests you need to run the script we added in the `package.json` earlier.
`npm run features`

***"++Remember to commit often++"***

Let's start with cleaning up `App.js` aka the App component. When that is done we want to add the inputs and selectors that we need to make this feature test to go green.
The App component should look like this:

```js
import React, { Component } from 'react';
import './App.css';

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

The test should now be able to find all the input fields which make it possible for it to assign values to them. At the moment all the steps in the `beforeEach` block in the feature test should run without any problem.

So now it is time for us to display the result. This is the time for us to do some component testing because we need to create a new component that returns the result to us.

Run this in the terminal:

```shell
$ npm i -D enzyme enzyme-adapter-react-16 react-scripts react-test-renderer sinon
$ touch src/setupTests.js
$ mkdir src/__tests__
$ touch src/__tests__/DisplayCooperResult.test.js
```

Add this to the files we added:

```js
// setupTests.js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() }); 
```

```js
// DisplayCooperResult.test.js
import React from 'react';
import { shallow } from 'enzyme';

import DisplayCooperResult from '../DisplayCooperResult';

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

If you run the application now, it will complain that it can't find the `DisplayCooperResult` module. Let's create it:

`$ mkdir src/Components`

`$ touch src/Components/DisplayCooperResult.js`

To start with, we will add a stateful/class component with a `render` method`. We will expand it with more functionality as we move ahead.
The file should look something like this:

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

When you run the test now you should get another error. The new error states that our expectation is returning false. So the component is not returning what it is supposed to when it gets the values from the input fields. It is now time to add some logic to our `DisplayCooperResult` component:

```js
import React, { Component } from 'react';
import cooperCalculator from '../Modules/CooperCalculator';

class DisplayCooperResult extends Component {

  calculate() {
    return cooperCalculator(this.props.distance, this.props.gender, this.props.age);
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

If the props the component receives are not empty, it will set the results variable we declared above the if statement with some HTML code. In the HTML code we display the `age`, `gender` and `distance`, but we also call on a new function called `calculate()`. This one is defined above the render method. This is very similar to what we did in the previous BMI Calculator application. We have extracted the actual calculation to a separate logic module. Let's go ahead and create that:

`$ mkdir src/Modules`

`$ touch src/Modules/CooperCalculator.js`

Add this code in there:

```js
export const cooperCalculator = (distance, gender, age) => {

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

export default cooperCalculator;
```

If you run the component test now, both tests should go green. Let's run the feature tests again, everything should go green right. We have a component that displays the correct result.

BUT, as you can see when we run the feature tests, nothing has changed. That's because we haven't actually rendered the `DiplayCooperResult` component in the `App` component. We haven't passed in any props to the `DisplayCooperResult` component. We haven't even saved what gets written in the input fields. So the actual `DiplayCooperResult` component works, but not with the rest of the application. This is the reason they are called component tests.

Let's add some code to the `App` component.

```js
import React, { Component } from 'react';
import './App.css';
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

Now we need to clean up the `App` component, we dont want to have the input fields here. The `App` component should be as clean as possible, that is why we will extract the input fields to their own component.

`$ touch src/Components/InputFields.js`

So all the input fields we have in the `App` component will be laced here instead:

```js
import React, { Component } from 'react';

class InputFields extends Component {
  render() {
    return (
      <>
        <label>Distance</label>
        <input id="distance" onChange={this.props.inputChangeHandler}></input>

        <select id="gender" onChange={this.props.inputChangeHandler}>
          <option value="female">Female</option>
          <option value="male">Male</option>
        </select>

        <label>Age</label>
        <input id="age" onChange={this.props.inputChangeHandler}></input>
      </>
    )
  }
}

export default InputFields;
```

We need to render this new component in the `App` component. First, we need to import it:
```js
import InputFields from './Components/InputFields';
```
Then we need to render the `InputFields` component and pass in the onChange method as a prop and bind it. So everytime the value of the input fields change the correlating state changes as well in the `App` component.

```js
// App.js
render() {
  // ...
  return (
  <InputFields 
    inputChangeHandler={this.onChange.bind(this)}
  />

  // ...
  )
}
```

You need to run the tests again to make sure that they still go green!
