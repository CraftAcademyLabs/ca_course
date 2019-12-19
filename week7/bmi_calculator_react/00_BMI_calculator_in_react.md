As the first challenge of this week, we will be returning to the BMI calculator. You have build this application once before, in Vanilla JavaScript. This means that you are familiar with the domain and the requirements.

This time around, we will build the BMI calculator in React AND get an introduction to testing React applications.

In this challenge, we will flip the process and write implementation code first and test afterwards. We will NOT follow the Acceptance - Unit Test Cycle (that will come later)

## Getting started

First off, we need to create the react application. we will use `create-react-app` to do so. When we scaffold our react application with it we get the basic project structure as well as Jest (the testing framework) partially configured.

Run this command in the terminal to create a React application:

`npx create-react-app bmi_calculator`

Go inside of the project and run `yarn start` or `npm start`to fire app the local server and navigate to `localhost:3000` to see that everything works fine.

## Cleaning

Start off with cleaning the `src/App.js`

Remove everything that is in the div with the class name `"App"`. Also, remove the import of the logo at line two.

The file should look like this:
```
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>

      </div>
    );
  }
}

export default App;
```
## Data input

Our user needs to input his data so that the application can return their BMI. We need to setup input fields for:

*   How much they weigh
*   How tall they are

So let's start with adding two input fields for this.

The file should look like this:
```
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>
        <div>
          <label>Weight(kg)</label>
          <input name="weight" />
        </div>

        <div>
          <label>Height(cm)</label>
          <input name="height" />
        </div>
      </div>
    );
  }
}

export default App;
```
## State

We need to save what we input into these fields. This is a great place to use state.

Update the input fields to look like this.
```
<div>
  <label>Weight(kg)</label>
  <input name="weight" value={this.state.weight} onChange={ (e) => this.setState({ weight: e.target.value })} />
</div>

<div>
  <label>Height(cm)</label>
  <input name="height" value={this.state.height} onChange={ (e) => this.setState({ height: e.target.value })} />
</div>
```
We also need to add a constructor above our render function to declare the state and the default value of our states.

The App.js file should look like this:
```
import React, { Component } from 'react';

class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      weight: '',
      height: ''
    }
  }

  render() {
    return (
      <div>
        <div>
          <label>Weight(kg)</label>
          <input name="weight" value={this.state.weight} onChange={ (e) => this.setState({ weight: e.target.value })} />
        </div>

        <div>
          <label>Height(cm)</label>
          <input name="height" value={this.state.height} onChange={ (e) => this.setState({ height: e.target.value })} />
        </div>
      </div>
    );
  }
}

export default App;
```
So, every time we change the value of the input, it will set the state to that value.

So now let's use those numbers to calculate the BMI of the person.

We are going to create a new component that displays the result.

Run this in the terminal:
```
$ mkdir src/Components
$ touch src/Components/displayResult.js
```

First, we need to set up everything that a react component needs.

The file should look like this:
```
import React, { Component } from 'react';

class DisplayResult extends Component {

  render() {
    return (
      <div id='response'>
      </div>
    )
  }
}

export default DisplayResult
```

Now we need to make sure to send the data from the App.js component to this new DisplayResult component. So back in the `App.js` file, we need to import the new component and render it.

The file should look like this:
```
import React, { Component } from 'react';
import DisplayResult from './Components/displayResult';

class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      weight: '',
      height: ''
    }
  }

  render() {
    return (
      <div>
        <div>
          <label>Weight(kg)</label>
          <input name="weight" value={this.state.weight} onChange={ (e) => this.setState({ weight: e.target.value })} />
        </div>

        <div>
          <label>Height(cm)</label>
          <input name="height" value={this.state.height} onChange={ (e) => this.setState({ height: e.target.value })} />
        </div>

        <DisplayResult
          weight={this.state.weight}
          height={this.state.height}
        />
      </div>
    );
  }
}

export default App;
```

As you can see we now pass in the state of both the weight and height to the `DisplayResult` component.

Let's go back to that component now.

Let's add a `calculate` function in the `DisplayResult` component:
```
import React, { Component } from 'react';

class DisplayResult extends Component {
  calculate() {
    var weight= this.props.weight;
    var height= this.props.height;
  }

  render() {
    return (
      <div id='response'>
        {this.calculate()}
      </div>
    )
  }
}

export default DisplayResult
```

So here we fetch the state that was exported from the `App.js`. It was sent to this component as props and in this `DisplayComponent` we will store it in variables as you can see.

## The logic

Now we want to create a logic module for the actual calculation part. We want to keep the component clean that is why we extract it to a separate file.

Run this in the terminal:
```
$ mkdir src/Modules
$ touch src/Modules/BMICalculator.js
```

This is going to be a pure vanilla javascript file with no mention of React. You have written this function in another application before in the boot camp. You can either use this code snippet we give you or reuse your old code, just remember to use the `export` keyword in front of the function you want to be able to access from outside of this file.

Write this code in your newly created file:
```
export const bmiCalculation = (weight, height, method) => {
  parseFloat(weight);
  parseFloat(height);
  let bmi;

  weight = isNaN(weight) ? 0 : weight;
  height = isNaN(height) ? 0 : height;

  bmi = weight / (height / 100 * height / 100);

  let finalBMI = parseFloat(bmi.toFixed(2));
  let BMIMessage = setBMIMessage(finalBMI)
  if (isNaN(finalBMI) || !isFinite(finalBMI) || finalBMI === 0) {
    return '';
  } else {
    return `You are ${BMIMessage} with a BMI of ${finalBMI}`;
  }      
}

const setBMIMessage = (finalBMI) => {
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
}
```

Now you want to use this in your calulate function in the `DisplayResult` component. Add this to it:
```
import React, { Component } from 'react';
import { bmiCalculation } from '../Modules/BMICalculator';

class DisplayResult extends Component {
  calculate() {
    var weight= this.props.weight;
    var height= this.props.height;

    return bmiCalculation(weight, height);
  }

  render() {
    return (
      <div id='response'>
        {this.calculate()}
      </div>
    )
  }
}

export default DisplayResult
```

## Wrap up

At this point, you should have a fully functional but unstyled BMI Calculator. This is a good opportunity for you to enhance your styling skills within a react application.