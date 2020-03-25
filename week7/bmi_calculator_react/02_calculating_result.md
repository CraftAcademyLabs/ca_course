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

