# Cooper client in React

## Setup

First, we need to create an application. Run this command in the terminal to create a React application using the `create-react-app` generator:

`npx create-react-app cooper_client`

We are going to set up the acceptance testing first (aka feature tests). Like we did in the BMI Calculation project, we are going to use `Cypress`.

Run this in the terminal:

`yarn -D add cypress`

That's it. No, really! we are done here.

Once we are done with the installation, we can launch cypress by

`./node_modules/.bin/cypress open`

The next step is to add a script to the `package.json` file to be able to run feature tests. We also want to modify the `start` script so the application runs on `localhost://3001`, the reason for this is that we want to be able to both run the Rails backend and this client at the same time.

```json
// package.json

 "scripts": {
    "start": "PORT=3001 react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "cy:open": "cypress open"
  },
```

And configure cypress:

```json
// cypress.json

{
  "baseUrl": "http://localhost:3001"
}
```

Then we can open cypress by `yarn run cy:open`

Once we run cypress for the first time, it will create a folder for us, with a lot of example tests (Have a look at them, to get a sense of how tests should be written).

Our tests are stored in `integration` folder and `fixtures` folder will contain JSON files, which we will introduce during the course of this document.

Also we will set up our component testing solution:

We will store our component tests in a separate folder (called `__tests__`) and use a different set of testing frameworks.

Let's go through the setup by creating folders and adding dependencies. Execute the following commands in your terminal:

```
$ yarn add -D enzyme enzyme-adapter-react-16 react-test-renderer
```

Add this to the `setupTests.js` file:

```js
// src/setupTests.js

import { configure } from "enzyme";
import Adapter from "enzyme-adapter-react-16";

configure({ adapter: new Adapter() });
```

Now we are all set up it's time to start working on our project!

## Acceptance tests

It is time to add our first test. Create the following feature file:

`$ touch cypress/integration/userCanGetCooperCalculationResult.spec.js`

In the first test, we will make sure that the user can input his/her data and get the correct result from the application. Add the following configuration and features:

```js
// cypress/integration/userCanGetCooperCalculationResult.spec.js

/// <reference types="Cypress" />

describe("Cooper Client calculates successfully", () => {
  it("calculates cooper performance", () => {
    cy.visit("/");
    cy.get("input#distance").type("1000");
    cy.get("select#gender").select("female");
    cy.get("input#age").type("23");
    cy.get("p#cooper-message").should(
      "contain",
      "23 y/o female running 1000 meters."
    );
    cy.get("p#cooper-result").should("contain", "Result: Poor");
  });
});
```

This is a pretty straight forward test. The user fills in the distance, selects their gender and types in their age. When all of these inputs have values we should get a response with their result.

To execute the feature tests you need to run the script we added in the `package.json` earlier. `yarn run cy:open`

**_Remember to commit often_**

The `create-react-app` scaffolds a lot of code for us. We don't need parts of it. Let's start with cleaning up `App.js` file that contains the App component. When we are done, we want to add our own code to it and define the inputs and selectors that we need to make our feature test go green.

We can safely remove/delete the following files:

- `App.css`
- `App.test.js`

Change the imports and the `render` function of the App component to look like this:

```js
// src/App.jsx

import React, { Component } from "react";

class App extends Component {
  render() {
    return (
      <>
        <label>Distance</label>
        <input name="distance" id="distance"></input>

        <select name="gender" id="gender">
          <option value="female">Female</option>
          <option value="male">Male</option>
        </select>

        <label>Age</label>
        <input name="age" id="age"></input>
      </>
    );
  }
}

export default App;
```

The test should now be able to find all the input fields that we need to fill in.

## Component tests

Now it is time for us to display the results of calculations based on the data the user has submitted. We will move away from the acceptance tests for a moment, and focus on testing our components. Especially the component we will create for displaying the results.

```
$ mkdir src/__tests__
$ touch src/__tests__/DisplayCooperResult.spec.js
```

And update your spec file like this:

```js
// src/__tests__/DisplayCooperResult.spec.js

import React from "react";
import { shallow } from "enzyme";

import DisplayCooperResult from "../components/DisplayCooperResult";

describe("<DisplayCooperResult />", () => {
  it("evaluates the correct result for female/poor", () => {
    const wrapper = shallow(
      <DisplayCooperResult distance="1000" gender="female" age="23" />
    );
    expect(wrapper.find("p#cooper-message").text()).toEqual(
      "23 y/o female running 1000 meters."
    );
    expect(wrapper.find("p#cooper-result").text()).toEqual("Result: Poor");
  });

  it("evaluates the correct result for female/average", () => {
    const wrapper = shallow(
      <DisplayCooperResult distance="2000" gender="female" age="23" />
    );
    expect(wrapper.find("p#cooper-message").text()).toEqual(
      "23 y/o female running 2000 meters."
    );
    expect(wrapper.find("p#cooper-result").text()).toEqual("Result: Average");
  });
});
```

After doing the BMI Calculator we know how to send in props to a child component. We send in `distance`, `gender` and `age`. Then we expect to get a result that the component renders.

If you run the component tests now (using the `yarn test` command in the terminal), you will see that the test complains that it can't find the `DisplayCooperResult` module. Let's create it:

```
$ mkdir src/components
$ touch src/components/DisplayCooperResult.jsx
```

To start with, we will add a functional component because we will not handle state in it. State will be handled by it's container `App.js`.

The component should look like this:

```js
// src/components/DisplayCooperResult.jsx

import React from "react";

const DisplayCooperResult = props => {
  return <div></div>;
};

export default DisplayCooperResult;
```

When you run the component tests now you should get another error. The new error states `Method “text” is meant to be run on 1 node. 0 found instead.`. That means that the `find()` function does not return anything and when you try to run the `text()` function on nothing it will error out. We have to add those elements so that our tests can find them and see what values are in them.

## Adding logic

It is now time to add these elements and some logic to our `DisplayCooperResult` component. We will store that logic in a separate file in a folder we will create for that very purpose. Please look carefully at the imports below, to figure out where that is.

```js
// src/components/DisplayCooperResult.jsx

import React from "react";
import cooperCalculator from "../modules/cooperCalculator";

const DisplayCooperResult = ({ distance, gender, age }) => {
  const calculate = () => {
    return cooperCalculator(distance, gender, age);
  };

  const propsPassed = distance && age ? true : false;

  return (
    <>
      {propsPassed && (
        <>
          <p id="cooper-message">
            {age} y/o {gender} running {distance} meters.
          </p>
          <p id="cooper-result">Result: {calculate()}</p>
        </>
      )}
    </>
  );
};

export default DisplayCooperResult;
```

There are couple of tricks introduced in this component:

- You can either receive props by adding the `props` argument to your functional component or you can use an ES6 feature called [destructuring the props](https://www.freecodecamp.org/news/the-basics-of-destructuring-props-in-react-a196696f5477/). What that does is picks from the props the attributes you mention. It is advantageous for a couple of reasons:
  - It reduces syntax, you don't have to write `props` everywhere.
  - Gives you more control over what you receive in your component.
  - Allows you to set default arguments (`age=20` for eg.)
- We add the `calculate()` function that will calculate our result and returns a message. That will be executed when the props change. If we do it like this we don't have to click on a submit button, anytime we change the inputs the message will automatically adapt.
- We extract a conditional calling it `propsPassed` where we check whether both `distance` and `age` are not empty strings.
- We use the shorter version of the ternary operator the `&&` which renders our content if `propsPassed` is true.
- When we use ternary operators inside jsx we always have to have one node that is wrapping all of our content. This could lead us have way to many nodes in our application so to bypass that we can use a [React.Fragment](https://reactjs.org/docs/fragments.html) which is recognized by react as a wrapper but it will not be an actual html element when we render the dom. The shorter version of that is `<> </>` which we are using in our return statement.

However, when you run your test, you will see the following error message (or something similiar):

```
  FAIL  src/__tests__/DisplayCooperResult.spec.js
  ● Test suite failed to run

    Cannot find module '../modules/cooperCalculator' from 'DisplayCooperResult.js'
    However, Jest was able to find:
    	'../components/DisplayCooperResult.jsx'

    You might want to include a file extension in your import, or update your 'moduleFileExtensions', which is currently ['web.js', 'js', 'web.ts', 'ts', 'web.tsx', 'tsx', 'json', 'web.jsx', 'jsx', 'node'].

    See https://jestjs.io/docs/en/configuration#modulefileextensions-array-string

      1 | import React from "react";
    > 2 | import CooperCalculator from "..//cooperCalculator";
        | ^
      3 |
      4 | const DisplayCooperResult = ({ distance, gender, age }) => {
      5 |   const calculate = () => {

      at Resolver.resolveModule (node_modules/jest-resolve/build/index.js:259:17)
      at Object.<anonymous> (src/components/DisplayCooperResult.jsx:2:1)

Test Suites: 1 failed, 1 total
Tests:       0 total
Snapshots:   0 total
Time:        0.576s, estimated 1s
Ran all test suites related to changed files.

Watch Usage: Press w to show more.
```

We are telling the component to import the logic from a module to which we extracted the actual calculation, but we have not created that file yet. Let's go ahead and do that:

`$ mkdir src/modules`

`$ touch src/modules/cooperCalculator.js`

Add this code in there:

```js
const CooperCalculator = (distance, gender, age) => {
  const ratings = [
    "Excellent",
    "Above average",
    "Average",
    "Below average",
    "Poor"
  ];

  const cooperTable = {
    female: {
      "13-14": [">2000", "1900-1999", "1600-1899", "1500-1599", "<1500"],
      "15-16": [">2100", "2000-2099", "1700-1999", "1600-1699", "<1600"],
      "17-19": [">2300", "2100-2299", "1800-2099", "1700-1799", "<1700"],
      "20-29": [">2700", "2200-2699", "1800-2199", "1500-1799", "<1500"],
      "30-39": [">2500", "2000-2499", "1700-1999", "1400-1699", "<1400"],
      "40-49": [">2300", "1900-2299", "1500-1899", "1200-1499", "<1200"],
      "50+": [">2200", "1700-2199", "1400-1699", "1100-1399", "<1100"]
    },
    male: {
      "13-14": [">2700", "2400-2699", "2200-2399", "2100-2199", "<2100"],
      "15-16": [">2800", "2500-2799", "2300-2499", "2200-2299", "<2200"],
      "17-19": [">3000", "2700-2999", "2500-2699", "2300-2499", "<2300"],
      "20-29": [">2800", "2400-2799", "2200-2399", "1600-2199", "<1600"],
      "30-39": [">2700", "2300-2699", "1900-2299", "1500-1999", "<1500"],
      "40-49": [">2500", "2100-2499", "1700-2099", "1400-1699", "<1400"],
      "50+": [">2400", "2000-2399", "1600-1999", "1300-1599", "<1300"]
    }
  };

  const ageRangeTable = age => {
    switch (true) {
      case age >= 13 && age <= 14:
        return "13-14";
      case age >= 15 && age <= 16:
        return "15-16";
      case age >= 17 && age <= 19:
        return "17-19";
      case age >= 20 && age <= 29:
        return "20-29";
      case age >= 30 && age <= 39:
        return "30-39";
      case age >= 40 && age <= 49:
        return "40-49";
      case age >= 50:
        return "50+";
      default:
        return "invalid range";
    }
  };

  const ageRange = ageRangeTable(parseInt(age));

  if (ageRange === "invalid range") {
    return "Invalid age range";
  }

  const distanceRanges = cooperTable[gender.toLowerCase()][ageRange];

  let ratingIndex;

  distanceRanges.forEach((dRange, index) => {
    if (
      (dRange.match(/>\d*/) && distance >= parseInt(dRange.slice(1), 10)) ||
      (dRange.match(/<\d*/) && distance < parseInt(dRange.slice(1), 10))
    ) {
      ratingIndex = index;
    } else {
      const minMax = dRange.split("-");
      const min = parseInt(minMax[0], 10);
      const max = parseInt(minMax[1], 10);

      if (distance >= min && distance <= max) {
        ratingIndex = index;
      }
    }
  });
  return ratings[ratingIndex];
};

export default CooperCalculator;
```

If you run the component test now, both tests should go green. Let's run the feature tests again, everything should go green right. We have a component that displays the correct result. If not then go over your code carefully again.

BUT, as you can see when we run the feature tests, nothing has changed. That's because we haven't actually rendered the `DiplayCooperResult` component in the `App` component. Consequently, we haven't passed in any props to the `DisplayCooperResult` component. At the moment the application is not storing what gets written in the input fields. So, the actual `DiplayCooperResult` component works, but not with the rest of the application. This is the reason they are called component tests.

## Connecting our app

Let's add some code to the `App` component.

```js
// src/App.jsx

import React, { Component } from "react";
import DisplayCooperResult from "./components/DisplayCooperResult";

class App extends Component {
  state = {
    distance: "",
    gender: "female",
    age: ""
  };

  onChangeHandler = e => {
    this.setState({ [e.target.name]: e.target.value });
  };

  render() {
    return (
      <>
        <label>Distance</label>
        <input
          onChange={this.onChangeHandler}
          name="distance"
          id="distance"
        ></input>

        <select onChange={this.onChangeHandler} name="gender" id="gender">
          <option value="female">Female</option>
          <option value="male">Male</option>
        </select>

        <label>Age</label>
        <input onChange={this.onChangeHandler} name="age" id="age"></input>
        <DisplayCooperResult
          distance={this.state.distance}
          gender={this.state.gender}
          age={this.state.age}
        />
      </>
    );
  }
}

export default App;
```

If you run the test now everything should go green!

## Refactoring

Now we need to clean up the `App` component, we don't want to have the input fields there. The `App` component should be as clean as possible, that is why we will extract the input fields to their own component.

The main goal of that component will be to handle the `onChange` event so we can create this test for the InputFields:

`$ touch src/__tests__/InputFields.spec.js`

```js
// src/__tests__/InputFields.spec.js

import React from "react";
import { shallow } from "enzyme";

import InputFields from "../components/InputFields";

describe("InputFields", () => {
  const onChangeHandler = jest.fn();
  const wrapper = shallow(<InputFields onChangeHandler={onChangeHandler} />);

  it("onChange functions is being on distance input", () => {
    wrapper
      .find("input#distance")
      .simulate("change", { target: { name: "distance", value: "2000" } });
    expect(onChangeHandler).toHaveBeenCalled();
  });

  it("onChange functions is being on gender select", () => {
    wrapper
      .find("select#gender")
      .simulate("change", { target: { name: "gender", value: "male" } });
    expect(onChangeHandler).toHaveBeenCalled();
  });

  it("onChange functions is being on age input", () => {
    wrapper
      .find("input#age")
      .simulate("change", { target: { name: "age", value: "28" } });
    expect(onChangeHandler).toHaveBeenCalled();
  });
});
```

And then we can create the component:

`$ touch src/Components/InputFields.js`

Make sure to remove the input fields from the `App` component and add this to the new `InputFields` component:

```js
// src/components/InputFields.jsx

import React from "react";

const InputFields = ({ onChangeHandler }) => {
  return (
    <>
      <label>Distance</label>
      <input onChange={onChangeHandler} name="distance" id="distance"></input>

      <select onChange={onChangeHandler} name="gender" id="gender">
        <option value="female">Female</option>
        <option value="male">Male</option>
      </select>

      <label>Age</label>
      <input onChange={onChangeHandler} name="age" id="age"></input>
    </>
  );
};

export default InputFields;
```

We need to import this new component into the `App` component before we can use it.

`import InputFields from './Components/InputFields';`

Then we need to render the `InputFields` component and pass in the onChange method as a prop and bind it. So every time the value of the input fields change the correlating state changes as well in the `App` component.

```js
// App.js
import React, { Component } from "react";

import DisplayCooperResult from "./components/DisplayCooperResult";
import InputFields from "./components/InputFields";

class App extends Component {
  state = {
    distance: "",
    gender: "female",
    age: ""
  };

  onChangeHandler = e => {
    this.setState({ [e.target.name]: e.target.value });
  };

  render() {
    return (
      <>
        <InputFields onChangeHandler={this.onChangeHandler} />
        <DisplayCooperResult
          distance={this.state.distance}
          gender={this.state.gender}
          age={this.state.age}
        />
      </>
    );
  }
}

export default App;
```

You need to run the tests again to make sure that they still go green!
