As the first challenge of this week, we will be returning to the BMI calculator. You have build this application once before, in Vanilla JavaScript. This means that you are familiar with the domain and the requirements.

This time around, we will build the BMI calculator in React and we will follow the Acceptance - Unit Test Cycle.

## Getting started

First off, we need to create the react application. we will use `create-react-app` to do so. When we scaffold our react application with it we get the basic project structure as well as Jest (the testing framework) partially configured.

Run this command in the terminal to create a React application:

`npx create-react-app bmi_calculator`

Go inside of the project and run `yarn start` to fire app the local server and navigate to `localhost:3000` to see that everything works fine.


## Configure Jest with Enzyme for Component testing

Now we are going to configure Jest so we can use it to unit test, or as we call it in React land, Component tests! We are going to use `jest`, `enzyme` to do this. So as usual, we need to add some packages. Run:

`yarn add enzyme enzyme-adapter-react-16 --dev`

Now we need to configure `enzyme`. Update the `setupTest.js` file to look like this:

```js
import { configure } from "enzyme";
import Adapter from "enzyme-adapter-react-16";

configure({ adapter: new Adapter() });
```

React automatically reads this file and will use enzyme as an adapter for running jest.

Enzyme exports 2 important functions for us:

- [Shallow](https://airbnb.io/enzyme/docs/api/shallow.html) this mounts the component and only the component. Children will not be mounted.
- [Render](https://airbnb.io/enzyme/docs/api/ReactWrapper/mount.html) this mounts the component and all child components.

Thats the configuration for Jest and Enzyme!

## Configure Acceptance testing

We are going to use [Cypress](https://www.cypress.io/) to write our acceptance test for this application.

### Installing Cypress

**Step1**: You need to run:

`npm i cypress --save-dev`

OR

`yarn add cypress --dev`

That's it. No, really! we are done here.

### Set Up Cypress

Once we are done with the installation, we can launch cypress by:

`yarn run cypress open`

But that's far too long, so we create a shortcut in our `package.json` by inserting `"cy:open": "yarn start --silent & cypress open"` in `scripts`.

If you use npm, the script should look like this `"cy:open": "npm start --silent & cypress open"`

Then we can open cypress by `npm run cy:open` or `yarn run cy:open`

Once we run cypress for first time, it will create scaffolded folder, with a lot of example tests (**Have a look at them, to get a sense of how tests should be written**).

Our tests are stored in `Integration` folder and `fixtures` folder will contain JSON files, which we will introduce during the course of this document. You can also delete the example tests in the `integration` folder.

Configure our `baseUrl` in `cypress.json` file:

```json
// cypress.json
{
  "baseUrl": "http://localhost:3000"
}
```

`baseUrl` is a something we can call on when we tell Cypress which URL it should go to. Instead of writing `http://localhost:3000`, we can call on that URL by writing `/`.

Now we have Cypress successfully configured.

## Cleaning

Start off with cleaning the `src/App.js`

Convert the file to a class, change the file extension to `jsx`, remove everything that is in the div with the class name `"App"`. Also, remove the import of the logo at line two.

The file should look like this:

```js
// src/App.jsx

import React, { Component } from "react";

class App extends Component {
  render() {
    return <div></div>;
  }
}

export default App;
```

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

## Adding the calculation

We have our form it is time to add the logic for submitting. For that we will use a helper function. It is good practice to keep our components clean and encapsulate and separate logic.

Add a helper folder with `bmiHelper.js` file:

```
mkdir src/helper
touch src/helper/bmiHelper.js
```

```js
export const calculateBmi = (weight, height) => {
  const bmiValue = (weight / (((height / 100) * height) / 100)).toFixed(2);
  const bmiMessage = setBMIMessage(bmiValue);
  return [bmiValue, bmiMessage];
};

const setBMIMessage = finalBMI => {
  if (finalBMI < 18.5) {
    return "Underweight";
  }

  if (finalBMI > 18.5 && finalBMI < 25) {
    return "Normal";
  }

  if (finalBMI > 25 && finalBMI < 30) {
    return "Overweight";
  }

  if (finalBMI > 30) {
    return "Obese";
  }
};
```

We need to calculate 2 values in this helper function:

- Our bmi value
- And the message assigned to that value
- We return these values using an ES6 feature

Now we need to use `calculateBmi`. Add the `onSubmitHandler` function to `App.jsx`:

```js
// src/App.jsx

import React, { Component } from "react";

import Form from "./components/Form";
import { calculateBmi } from "./helpers/bmiHelper";

class App extends Component {
  state = {
    weight: "",
    height: "",
    bmiValue: "",
    bmiMessage: ""
  };

  onChangeHandler = e => this.setState({ [e.target.name]: e.target.value });

  onSubmitHandler = e => {
    e.preventDefault();
    const [bmiValue, bmiMessage] = calculateBmi(
      this.state.weight,
      this.state.height
    );
    this.setState({ bmiValue: bmiValue, bmiMessage: bmiMessage });
  };

  render() {
    return (
      <div>
        <Form
          weight={this.state.weight}
          height={this.state.height}
          onChangeHandler={this.onChangeHandler}
          onSubmitHandler={this.onSubmitHandler}
        />
      </div>
    );
  }
}

export default App;
```

As with `onChange`, `onSubmit` receives the `event` object by default. First we need to call `preventDefault` on it because the default of html `form` will make an http post request. (to nowhere in this case because we haven't defined it). After that we use our state to pass it down to our helper function. Use some nice ES6 feature to grab the results and update our state.

To finish add the onSubmitHandler to the `Form.jsx` as well:

```js
// src/components/Form.jsx

import React from "react";

const Form = props => {
  return (
    <form onSubmit={props.onSubmitHandler}>
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

If you run the component test for the `Form` component, everything should go green!

## Showing the message
When you run cypress, you can see that we are not displaying the result of the calculation.

As we did with the form, we will extract the rendering of the message to a seperate component called `Message`.

Lets start out with creating a new component test.

```bash
$ src/__test__/Message.spec.js
```

```js
import React from "react";
import { shallow } from "enzyme";

import Message from "../components/Message";

describe("Message component", () => {
  const wrapper = shallow(<Message bmiMessage="Normal" bmiValue="23.59" />);

  it("renders with message prop", () => {
    expect(wrapper.find("#bmi-message").text()).toEqual(
      "You are Normal with a BMI of 23.59"
    );
  });
});
```

Here we use the function called `text()` to check what is the text inside our element.

Finally add another component called `Message.jsx`:

```bash
$ touch src/components/Message.jsx
```

```js
// src/components/Message.jsx

import React from "react";

const Message = props => {
  return (
    <p id='bmi-message'>
      You are {props.bmiMessage} with a BMI of {props.bmiValue}
    </p>
  );
};

export default Message;
```

And update the `App.jsx`:

```js
// src/App.jsx

import React, { Component } from "react";

import Form from "./components/Form";
import Message from "./components/Message";
import { calculateBmi } from "./helpers/bmiHelper";

class App extends Component {
  state = {
    weight: "",
    height: "",
    bmiValue: "",
    bmiMessage: ""
  };

  onChangeHandler = e => this.setState({ [e.target.name]: e.target.value });

  onSubmitHandler = e => {
    e.preventDefault();
    const [bmiValue, bmiMessage] = calculateBmi(
      this.state.weight,
      this.state.height
    );
    this.setState({ bmiValue: bmiValue, bmiMessage: bmiMessage });
  };

  render() {
    return (
      <div>
        <Form
          weight={this.state.weight}
          height={this.state.height}
          onChangeHandler={this.onChangeHandler}
          onSubmitHandler={this.onSubmitHandler}
        />
        {this.state.bmiValue && (
          <Message
            bmiValue={this.state.bmiValue}
            bmiMessage={this.state.bmiMessage}
          />
        )}
      </div>
    );
  }
}

export default App;
```

Here we do another little trick. We hide the message until we have a bmiValue in our state. We are using a shorter version of the ternary operator: `&&`. This means that `<Message/>` will only be rendered when `this.state.bmiValue` is not `""`. We pass down our relevant state values and have ourselves a message.

If you run the acceptance tests now you will see that everything passes. 

## Wrap up
At this point, you should have a fully functional but un-styled BMI Calculator. This is a good opportunity for you to enhance your styling skills within a react application, and also it is your task to add the imperial method to this calculation. The way you want to implement is totally up to you!

### Component tests
This is how you test the components. The primary goal of these tests modules of your program individually and to make sure that the logic of your program works as intended. As your application grows it will be harder to keep track of what change affects what. Having good component specs will help you see the effects of those changes on every component.

### Acceptance tests
Acceptance tests help you in making sure that your application does what it is supposed to do when a user interacts with it. It requires limited knowledge about the implementation itself (how each component works). Acceptance tests are just one of the testing strategies we will use when developing our React applications.

## Test components with mount

Now that we have tested our components with shallow it is time to test `App` and see if our logic works as it should. This is an example your test might be different based on how you have implemented the imperial method. In our example we have a selector to choose between the methods.

```js
//src/__tests__/App.test.js

import React from "react";
import { mount } from "enzyme";
import App from "../App";

describe("App component", () => {
  const wrapper = mount(<App />);
  it("Counts using the metric method", () => {
    wrapper
      .find("#select-method")
      .simulate("change", { target: { value: "metric" } });
    wrapper
      .find("#weight")
      .simulate("change", { target: { name: "weight", value: "90" } });
    wrapper
      .find("#height")
      .simulate("change", { target: { name: "height", value: "190" } });
    wrapper.find("form").simulate("submit");
    expect(wrapper.find("#bmi-message").text()).toEqual(
      "You are Normal with a BMI of 24.93"
    );
  });

  it("Counts using the imperial method", () => {
    wrapper
      .find("#select-method")
      .simulate("change", { target: { value: "imperial" } });
    wrapper
      .find("#weight")
      .simulate("change", { target: { name: "weight", value: "192" } });
    wrapper
      .find("#height")
      .simulate("change", { target: { name: "height", value: "74" } });
    wrapper.find("form").simulate("submit");
    expect(wrapper.find("#bmi-message").text()).toEqual(
      "You are Normal with a BMI of 24.65"
    );
  });
});
```


## Imperial method

I will show you a test case for the imperial method as an example. 

The testing of the actual method may change depending on the implementation that you have. As you can see it is pretty straight forward, we select the method from a selector, fill in the inputs with the relevant information and then expect what response is going to be shown on the page.

```js
// cypress/integration/examples/BMI.spec.js

/// <reference types="Cypress" />

describe('BMI Calculator', () => {
  beforeEach(() => {
    cy.visit('/');
  })

  it('Calculates BMI in metric',() => {
    cy.get('select#select-method').select('metric');
    cy.get('input#weight').type(90);
    cy.get('input#height').type(190);
    cy.get('button#calculate').click();
    cy.get('p#bmi-message').should('contain', 'You are Normal with a BMI of 24.93')
  })
  it('Calculates BMI in imperial',() => {
    cy.get('select#select-method').select('imperial');
    cy.get('input#weight').type(198);
    cy.get('input#height').type(74);
    cy.get('button#calculate').click();
    cy.get('p#bmi-message').should('contain', 'You are Overweight with a BMI of 25.42')
  })
})

```
