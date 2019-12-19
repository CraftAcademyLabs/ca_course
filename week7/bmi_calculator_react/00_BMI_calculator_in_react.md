As the first challenge of this week, we will be returning to the BMI calculator. You have build this application once before, in Vanilla JavaScript. This means that you are familiar with the domain and the requirements.

This time around, we will build the BMI calculator in React AND get an introduction to testing React applications.

In this challenge, we will flip the process and write implementation code first and test afterwards. We will NOT follow the Acceptance - Unit Test Cycle (that will come later)

## Getting started

First off, we need to create the react application. we will use `create-react-app` to do so. When we scaffold our react application with it we get the basic project structure as well as Jest (the testing framework) partially configured.

Run this command in the terminal to create a React application:

`npx create-react-app bmi_calculator`

Go inside of the project and run `yarn start` to fire app the local server and navigate to `localhost:3000` to see that everything works fine.

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

## Data input

Our user needs to input his data so that the application can return their BMI. We need to setup input fields for:

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

This gets the job done but keeping everything in one component could make it grow out of proportions. In React it is good practice to extract out parts of the UI into functional components. Lets add a `Form.jsx` file!

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
      <button>Calculate BMI</button>
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
      <button>Calculate BMI</button>
    </form>
  );
};

export default Form;

```

## Showing the message

Finally add another component called `Message.jsx`:

```
touch src/components/Message.jsx
```

```js
// src/components/Message.jsx

import React from "react";

const Message = props => {
  return (
    <p>
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

Here we do another little trick. We hide the message until we have a bmiValue in our state. We are using a shorter version of the ternary operator: `&&`. This means that `<Message/> ` will only be rendered when `this.state.bmiValue` is not `""`. We pass down our relevant state values and have ourselves a message.

## Wrap up

At this point, you should have a fully functional but unstyled BMI Calculator. This is a good opportunity for you to enhance your styling skills within a react application.
