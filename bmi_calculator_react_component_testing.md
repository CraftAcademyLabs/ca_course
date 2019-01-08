## Component testing

Now we are going to do some unit testing, or as we call them in React land, Component tests!
We are going to use `jest`, `enzyme` and `sinon` to do this. So as usuall, we need to add some packages. Run:

`npm i -D enzyme enzyme-adapter-react-16 react-scripts react-test-renderer sinon`

In the previous guide where we scaffolded this application with `create-react-app` it said that it already came with testing, but we need to add some stuff to be able to test it in the best way possible. That is why we need to add `enzyme` and `sinon`. This means that the only configuration that we need to do after adding these packages is for `enzyme`. Run:

`touch src/setupTests.js`

The file should look like this:

src/setupTests.js:                                                        

```js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() }); 
```

Now we need to create our test folder and our first test file.
Run:

`mkdir src/__tests__`

`touch src/__tests__/App.test.js`

Add all of the code underneath to `App.test.js` file.

```js
import React from 'react';
import { mount, shallow } from 'enzyme';
import { stub } from 'sinon';

import DisplayResult from './DisplayResult'
import MethodSelect from './MethodSelect';
import App from './App';

describe('<App />', () => {
  it('renders header', () => {
    const component = shallow(<App />);
    const header = <h1>BMI Converter</h1>;
    expect(component.contains(header)).toEqual(true);
  });

  it('shows metric as the standard method', () => {
    const component = shallow(<App />);
    const weightLabel = <label>Weight(kg)</label>;
    const heightLabel = <label>Height(cm)</label>;
    expect(component.contains(weightLabel)).toEqual(true);
    expect(component.contains(heightLabel)).toEqual(true);
  })

  it('can change method', () => {
    const onChangeValue = stub();
    const component = shallow(<App onChangeValue={onChangeValue} />);
    const weightLabel = <label>Weight(lbs)</label>;
    const heightLabel = <label>Height(inches)</label>;
    component.find("MethodSelect").prop('onChangeValue')({target: {value:'imperial'}});
    expect(component.contains(weightLabel)).toEqual(true);
    expect(component.contains(heightLabel)).toEqual(true);
  })
})

describe('<DisplayResult />', () => {
  it('displays the calulation correct(metric)', () => {
    const component = mount(<DisplayResult method='metric' weight='100' height='195'/>)
    const response = <div id='response'>You are Overweight with a BMI of 26.3</div>
    expect(component.contains(response)).toEqual(true)
  })

  it('displays the calulation correct(imperial)', () => {
    const component = mount(<DisplayResult method='imperial' weight='140' height='73'/>)
    const response = <div id='response'>You are Underweight with a BMI of 18.47</div>
    expect(component.contains(response)).toEqual(true)
  })

  it('does not show anything when one of the input fields are empty', () => {
    const component = mount(<DisplayResult method='metric' weight='' height='195'/>);
    expect(component.text()).toBe('')
  })
})

describe('<MethodSelect />', () => {
  it('has two methods to choose from', () => {
    const component = mount(<MethodSelect />);
    const selector = component.find('#method').instance()
    expect(selector.options.length).toEqual(2)
  }
)})
```

As we said in the guide for setting up the accaptance tests for the BMI Calculator application, this is how we test our app with our implementation. Yours might be a bit different. With the examples above you should be able to modifu them for your implmentation. 

Lets go through part of the examples above.
```js
import React from 'react';
import { mount, shallow } from 'enzyme';
import { stub } from 'sinon';

import DisplayResult from './DisplayResult';
import MethodSelect from './MethodSelect';
import App from './App';

```

Here we are importing everything that we need to test our application and what we we want to test. 

The first three are what we need to be able to test. `enzyme` we need to be able to create fake versions of the components that we want to test. `react` we need because we are testig a apllication built with the React library. `sinon` we need to have to be able to mock and stub out some of our functions that we have in our components.  

The last three imports that we have here are the components that we have in our application. This is one of the main differences between the acceptance testing we have done with `jest-puppeteer` and the component testing we are going over at the moment. With the acceptance testing we are testing the whole application, as the user will see it, but here we are going to test each component by itself. To make sure that they work properly by themselves. 

```js
describe('<App />', () => {
  it('renders header', () => {
    const component = shallow(<App />);
    const header = <h1>BMI Converter</h1>;
    expect(component.contains(header)).toEqual(true);
  });

  it('shows metric as the standard method', () => {
    const component = shallow(<App />);
    const weightLabel = <lashows>Weight(kg)</label>;
    const heightLabel = <label>Height(cm)</label>;
    expect(component.contains(weightLabel)).toEqual(true);
    expect(component.contains(heightLabel)).toEqual(true);
  })
```

So here we are testing the `App` component, as it states in the describe block.
The first test here is to make sure that the `App` component renders the header.
First we create the component with a method from enzyme that we imported called shallow. 

[Shallow rendering is useful to constrain yourself to testing a component as a unit, and to ensure that your tests aren't indirectly asserting on behavior of child components.](https://github.com/airbnb/enzyme/blob/master/docs/api/shallow.md)

After this we declare a variable and makes it equal a bit of HTML code. Then in the expect block we test for that the HTML code that we stored in the variable named `header` exists in the `App` component.


Next test is `shows metric as the standard method`. In our `App` component we have a constructor where we set the calculation method as a sate and it equals  to metric. This means that everytime someone opens the application, metric is going to be the default method. The way we have implemented our app, is that the label for the inputs change depending on wich method is selected. So if the labels are correct they should show "kg" and "cm" in the parenthesis. So as the test above we store what HTML code we expect to see in variables and then we test for them to be rendered by the `App` component.