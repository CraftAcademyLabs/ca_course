# BMI calculator in React

Firt of we need to create the react application. we will use `create-react-app` to do so. When we scaffold our react application with it we get the basic project structure aswell as jest partially configured. 

Run this in the terminal:

```shell
npx create-react-app bmi_calculator
```

Go inside of the project and run `yarn start` to see that everything works fine.

Start of with cleaning the `src/App.js`
Remove everything that is in the div with the class name `"App"`. Also remove the import of the logo at line two. 
The file should look like this:

```jsx
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        
      </div>
    );
  }
}

export default App;
```
So what is that we need for our user needs to input so that the application can return their BMI. We need:

* How much they weigh
* How tall they are

So lets start with adding two input fields for this.

The file should look like this:

```jsx
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
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
We need to save what we input into these fields. This is great place to use state
Update the input fields to look like this. 

```jsx
<div>
  <label>Weight(kg)</label>
  <input name="weight" value={this.state.weight} onChange={(e) => this.setState({ weight: e.target.value })} />
</div>

<div>
  <label>Height(cm)</label>
  <input name="height" value={this.state.height} onChange={(e) => this.setState({ height: e.target.value })} />
</div>
```

We also need to add a constructor above our render function to declare the state and hte default value of our states. The App.js file should look like this:

```jsx
import React, { Component } from 'react';
import './App.css';

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
      <div className="App">
        <div>
          <label>Weight(kg)</label>
          <input name="weight" value={this.state.weight} onChange={(e) => this.setState({ weight: e.target.value })} />
        </div>

        <div>
          <label>Height(cm)</label>
          <input name="height" value={this.state.height} onChange={(e) => this.setState({ height: e.target.value })} />
        </div>
      </div>
    );
  }
}

export default App;
```

So everytime we change the value of the input, it will set the state to that value.

So now lets use those numbers to actually calculate the BMI of the person.

We are going to create a new component that displays the result.
Run this in the terminal: 

`touch src/DisplayResult.js`

First of we need to setup everything that a react component needs.
The file should look like this:

```jsx
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

Now we need to make sure to send the data from the App.js component to this new DisplayResult component. So back in the `App.js` file we need to import the new component and render it. The file should look like this:

```jsx
import React, { Component } from 'react';
import './App.css';
import DisplayResult from './DisplayResult';

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
      <div className="App">
        <div>
          <label>Weight(kg)</label>
          <input name="weight" value={this.state.weight} onChange={(e) => this.setState({ weight: e.target.value })} />
        </div>

        <div>
          <label>Height(cm)</label>
          <input name="height" value={this.state.height} onChange={(e) => this.setState({ height: e.target.value })} />
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

As you can see we now pass in the state of both weight and height to the `DisplayResult` component. Lets go back to that component now. 

Lets add a calculate function in the `DisplayResult` component: 

```jsx
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

Now we want to create a logic module for the actual calculation part. We want to keep the component clean that is why we extract it to a seperate file.

Run this in the terminal:

`touch src/BMICalculator.js`

This is going to be a pure vanilla javascript file with no mention of react.

Write this code in your newly created file:

```js
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

Now you want to use this somehow in your calulate function in the `DisplayResult` component. Add this to it:

```jsx
import React, { Component } from 'react';
import { bmiCalculation } from './BMICalculator';


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