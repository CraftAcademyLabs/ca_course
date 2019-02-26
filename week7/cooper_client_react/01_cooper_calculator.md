# Cooper client in React

First we need to create the application. Run:

`npx create-react-app cooper_client`

We are going to setup the acceptance testing first. Like we did in the BMI Calculation project, we are going to use `jest-puppeteer`.

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

Now you need to add a script to the `package.json` to be able to run these tests

```js
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject",
  "test:features": "jest -c jest.config.js"
}
```
It is time to add our first test now. Run:

`$ mkdir src/__features__`

`$ touch src/__features__/CooperCalculation.feature.js`

Add these tests to the new feature file:

```js
describe('Cooper Client', async () => {
  beforeAll(async () => {
    await page.goto('http://localhost:3000');
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

It is a pretty straigth forward test. The user fills in the distance, selects their gender and types in their age. When all of these inputs have values we should get a response with their result. 

***"++Remember to commit often++"***

Lets start with cleaning up `App.js` aka the App component. When that is done we want to add the inputs and selectors that we need to make this feature test to go green.
The App component should look like this:

```js
import React, { Component } from 'react';
import './App.css';

class App extends Component {
 render() {
    return (
      <div className="App">
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

When running the test now, we should not get any errors `beforeEach` block that we have in our feature test. It now finds all the fields and input the correct values that we have set in the test.

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
import { mount, shallow } from 'enzyme';

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

If you run `npm run test` now it will complain that it cant find the `DisplayCooperResult` module. That is not that weird considering we dont have that component. Lets create it:

`$ touch src/DisplayCooperResult.js`

And add this to that file:

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
If you run the test again now you can see that you dont get the same error. Now we get error saying that the expected value is value, instead of true as we want. Lets add some code the new `DisplayCooperResult` component:

```js
import React, { Component } from 'react';
import { cooperCalculator } from './CooperCalculator';

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

If the props the component recieves is not empty, it will set the results varaiable we declared above the if statement with some HTML code. In the HTML code we display the the `age`, `gender` and `distance`, but we also call on a new function called `calculate()`. This one is defined above the render function. This is very similar to the BMI Calculator we have done before. We have extracted the actuall calculation to a seperate logic module. Lets go ahead and create that.

`$ touch src/CooperCalculator.js`

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
```

If you run the test now, both tests should go green. Lets run the feature tests again now, everything should go green right. We have a component that displays the correct result.

BUT, as you can see when we run the feature tests, nothing has changed. Thats because we haven't actually rendered the `DiplayCooperResult` component in the `App` component. We haven't passed in any props to the `DisplayCooperResult` component. We haven't even saved what gets written in the input fields. So the actual `DiplayCooperResult` component works, but not with rest of the application. This is the reason they are called component tests.

Lets add some code to the `App` component.

```js
import React, { Component } from 'react';
import './App.css';
import DisplayCooperResult from './DisplayCooperResult'

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
      <div className="App">
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