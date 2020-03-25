## Your first tests

Now it is time that we write our first test, which is going to be the scenario of a user calculating their BMI.

`$ touch cypress/integration/userCanCalculateTheirBMI.spec.js`

Read through the code (and comments) below and add it to the feature file you just created:
```js
// cypress/integration/examples/BMI.spec.js

/// <reference types="Cypress" />

describe('BMI Calculator', () => {
  it('Calculates BMI in metric',() => {
    cy.visit('/');
    cy.get('input#weight').type(90);
    cy.get('input#height').type(190);
    cy.get('button#calculate').click();
    cy.get('p#bmi-message').should('contain', 'You are Normal with a BMI of 24.93')
  })
})
```

This is a very simple test of our calculation, we just use our selectors to input some values and then press the button to see the response.

## Data input

It is now time to get these test to pass. Our user needs to input his data so that the application can return their BMI. We need to setup input fields for:

- How much they weigh in kgs
- How tall they are in cms

So let's start with adding two input fields for this.

The file should look like this:

```js
// src/App.jsx

import React, { Component } from "react";

class App extends Component {
  render() {
    return (
      <form>
        <label htmlFor="weight">Weight</label>
        <input
          type="number"
          required
          placeholder="Weight in kgs"
          name="weight"
          id="weight"
        />
        <label htmlFor="height">Height</label>
        <input
          type="number"
          required
          placeholder="Height in cm"
          name="height"
          id="height"
        />
        <button>Calculate BMI</button>
      </form>
    );
  }
}

export default App;
```

This gets the job done in regards to displaying some input fields, but keeping everything in one component could make it grow out of proportions. In React it is good practice to extract out parts of the UI into functional components. We need to add a `Form.jsx` file!

Before we create that component, we want to add a component test where we define what we need this new `Form` compoonent to do.

```bash
$ mkdir src/__tests__
$ touch src/__tests__/Form.spec.js
```

```js
// src/__tests__/Form.spec.js

import React from "react";
import { shallow } from "enzyme";

import Form from "./Form";

describe("Form", () => {
  const handleChange = jest.fn();
  const handleSubmit = jest.fn();
  const wrapper = shallow(
    <Form
      weight="90"
      height="190"
      onChangeHandler={handleChange}
      onSubmitHandler={handleSubmit}
    />
  );

  it("renders with weight prop", () => {
    expect(wrapper.find("#weight").props().value).toEqual("90");
  });

  it("renders with height prop", () => {
    expect(wrapper.find("#height").props().value).toEqual("190");
  });

  it("on change the onChangeHandler is being called", () => {
    wrapper.find("#weight").simulate("change");
    expect(handleChange).toHaveBeenCalled();
    wrapper.find("#height").simulate("change");
    expect(handleChange).toHaveBeenCalled();
  });

  it("on submit the onSubmitHandler is being called", () => {
    wrapper.find("form").simulate("submit");
    expect(handleSubmit).toHaveBeenCalled();
  });
});
```

So what we do here? :

- We shallow render `<Form />` and pass down the props. We use `jest.fn()` to create a mock functions for our functions. In this test we don't care what these functions are doing, we only care that they are being called. That is the job of that component.
- We pass down props to our `Form` and use enzyme to find these values using the `.find().props()` functions on our wrapper.
- Lastly we simulate change and submit events to check whether our functions are called or not.

So our `Form` component will have two input fields, one for weight and one for height. These two input fields will have a value, whatever the user type into that input field.

Everytime we change the input fields we want to send off the value to the app component and store that in state. That is what we are testing at the `on change the onChangeHandler is being called` it block.

When we want to see the result of the BMI calculator, the user will click on a submit button. That will call on a function in the `App` component. That is what we are testing at the `on submit the onSubmitHandler is being called`.

## Adding a Form Component
```
$ mkdir src/components
$ touch src/components/Form.jsx
```

```js
// src/components/Form.jsx

import React from "react";

const Form = props => {
  return (
    <form>
      <label htmlFor="weight">Weight</label>
      <input
        type="number"
        required
        placeholder="Weight in kgs"
        name="weight"
        id="weight"
      />
      <label htmlFor="height">Height</label>
      <input
        type="number"
        required
        placeholder="Height in cm"
        name="height"
        id="height"
      />
      <button>Calculate BMI</button>
    </form>
  );
};

export default Form;
```

And update `App.jsx` like this:

```js
// src/App.jsx

import React, { Component } from "react";
import Form from "./components/Form";

class App extends Component {
  render() {
    return <Form />;
  }
}

export default App;
```

## State

Now that we have our form we could use react state to store the information. To do that we need to do the following:

- Initiate state in our `App.jsx`.
- Add a function that handles changes in our input fields and saves the input.
- Pass down the state and the function to our `Form.jsx`.

Update the files to look like this:

```js
// src/App.jsx

import React, { Component } from "react";

import Form from "./components/Form";

class App extends Component {
  state = {
    weight: "",
    height: ""
  };

  onChangeHandler = e => this.setState({ [e.target.name]: e.target.value });

  render() {
    return (
      <div>
        <Form
          weight={this.state.weight}
          height={this.state.height}
          onChangeHandler={this.onChangeHandler}
        />
      </div>
    );
  }
}

export default App;
```

```js
// src/components/Form.jsx

import React from "react";

const Form = props => {
  return (
    <form>
      <label htmlFor="weight">Weight</label>
      <input
        type="number"
        required
        placeholder="Weight in kgs"
        value={props.weight}
        name="weight"
        id="weight"
        onChange={props.onChangeHandler}
      />
      <label htmlFor="height">Height</label>
      <input
        type="number"
        required
        placeholder="Height in cm"
        value={props.height}
        name="height"
        id="height"
        onChange={props.onChangeHandler}
      />
      <button id='calculate'>Calculate BMI</button>
    </form>
  );
};

export default Form;
```

Quick explanation what is happening here:

- We are using the change event provided by input to set the state. That is received by the function automatically.
- `[e.target.name]` is the syntax for using dynamic keys in JavaScript objects.
- When setting the state `App.jsx` is re-rendered, causing the `Form.jsx` to re-render because it receives the state as prop. The input value is set to the state and the inputted data will be visible on screen.