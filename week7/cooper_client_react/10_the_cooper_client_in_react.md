First, we need to create an application. Run this command in the terminal to create a React application using the `create-react-app` generator:

`npx create-react-app cooper_client`

We are going to set up the acceptance testing first (aka feature tests). Like we did in the BMI Calculation project, we are going to use `Cypress`.

Run this in the terminal:

`npm i cypress --save-dev`

That's it. No, really! we are done here.

Once we are done with the installation, we can launch cypress by

`./node_modules/.bin/cypress open`

The next step is to add a script to the `package.json` file to be able to run feature tests. We also want to modify the `start` script so the application runs on `localhost://3001`, the reason for this is that we want to be able to both run the Rails backend and this client at the same time.
```
"scripts": {
    "start": "PORT=3001 react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "features": "jest -c jest.config.js"
},
```

Then we can open cypress by `npm run cy:open`

Once we run cypress for the first time, it will create a folder for us, with a lot of example tests (Have a look at them, to get a sense of how tests should be written).

Our tests are stored in `integration` folder and `fixtures` folder will contain JSON files, which we will introduce during the course of this document.

It is time to add our first test. Create the following feature file:

`$ touch cypress/integration/userCanGetCooperCalculationResult.spec.js`

In the first test, we will make sure that the user can input his/her data and get the correct result from the application. Add the following configuration and features:
```
describe('Cooper Client calculates successfully', () => {
  before(function() {
    cy.visit('http://localhost:3001');
    cy.get('input[id="distance"]').type('1000')
    cy.get('select[id="gender"]').select('female')
    cy.get('input[id="age"]').type('23')
  })

  it('displays age', () => {
    cy.contains('23 y/o')
  })

  it('displays gender', () => {
    cy.contains('female')
  })

  it('displays distance', () => {
    cy.contains('running 1000 meters')
  })

  it('displays result', () => {
    cy.contains('Result: Poor')
  })
})
```

This is a pretty straight forward test. The user fills in the distance, selects their gender and types in their age. When all of these inputs have values we should get a response with their result.

To execute the feature tests you need to run the script we added in the `package.json` earlier. `npm run cy:open`

**_Remember to commit often and to run `npm install` after we add packages_**

The `create-react-app` scaffolds a lot of code for us. We don't need parts of it. Let's start with cleaning up `App.js` file that contains the App component. When we are done, we want to add our own code to it and define the inputs and selectors that we need to make our feature test go green.

We can safely remove/delete the following files:

*   `App.css`
*   `App.test.js`

Change the imports and the `render` function of the App component to look like this:
```
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

The response should look something like this in the terminal. If not, then go over the config again.

![text](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/week7/cooper_client_react/React_cooper_client_test_response.png)

Now it is time for us to display the results of calculations based on the data the user has submitted. We will move away from the acceptance tests for a moment, and focus on testing our components. Especially the component we will create for displaying the results.

We will store our component tests in a separate folder (called `__tests__`) and use a different set of testing frameworks.

Let's go through the setup by creating folders and adding dependecies. Execute the following commands in your terminal:
```
$ npm i -D enzyme enzyme-adapter-react-16 react-test-renderer
$ touch src/setupTests.js
$ mkdir src/__tests__
$ touch src/__tests__/displayCooperResult.test.js
```

Add this to the files we created above:
```
// setupTests.js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() }); 
```
```
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
```
$ mkdir src/Components
$ touch src/Components/DisplayCooperResult.js
```

To start with, we will add a stateful/class component with a `render` method. We will expand it with more functionality as we move ahead.

The component should look like this:
```
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

When you run the component tests now you should get another error. The new error states that our expectation/assertion is returning `false`. The component is not returning what it is supposed to when it gets the values from the input fields.

It is now time to add some logic to our `DisplayCooperResult` component. We will store that logic in a separate file in a folder we will create for that very purpose. Please look carefully at the imports below, to figure out where that is.
```
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
```
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
```
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

If you run the component test now, both tests should go green. Let's run the feature tests again, everything should go green right. We have a component that displays the correct result. If not then go over your code carefully again.

![text](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/week7/cooper_client_react/cooper_challenge_component_test_green.png)

BUT, as you can see when we run the feature tests, nothing has changed. That's because we haven't actually rendered the `DiplayCooperResult` component in the `App` component. Consequently, we haven't passed in any props to the `DisplayCooperResult` component. At the moment the application is not storing what gets written in the input fields. So, the actual `DiplayCooperResult` component works, but not with the rest of the application. This is the reason they are called component tests.

Let's add some code to the `App` component.
```
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

Make sure to remove the input fields from the `App` component and add this to the new `InputFields` component:
```
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

`import InputFields from './Components/InputFields';`

Then we need to render the `InputFields` component and pass in the onChange method as a prop and bind it. So everytime the value of the input fields change the correlating state changes as well in the `App` component.
```
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