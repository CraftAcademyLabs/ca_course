## Component testing 
 
 Now we are going to do some unit testing, or as we call them in React land, Component tests! We are going to use `jest`, `enzyme` and `sinon` to do this. So as usual, we need to add some packages. Run: 

`npm i -D enzyme enzyme-adapter-react-16 react-scripts react-test-renderer sinon` 

Now we need to configure `enzyme`. Run:
 
 `touch src/setupTests.js` 

The file should look like this:                                                        
```js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() }); 
```

Now we need to create our test folder and our first test file.
Run:

`$ mkdir src/__tests__`

`$ touch src/__tests__/App.test.js`

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
    const component = shallow(<DisplayResult method='metric' weight='100' height='195'/>)
    const response = <div id='response'>You are Overweight with a BMI of 26.3</div>
    expect(component.contains(response)).toEqual(true)
  })

  it('displays the calulation correct(imperial)', () => {
    const component = shallow(<DisplayResult method='imperial' weight='140' height='73'/>)
    const response = <div id='response'>You are Underweight with a BMI of 18.47</div>
    expect(component.contains(response)).toEqual(true)
  })

  it('does not show anything when one of the input fields are empty', () => {
    const component = shallow(<DisplayResult method='metric' weight='' height='195'/>);
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

As we said in the guide for setting up the acceptance tests for the BMI Calculator application, this is how we test our app with our implementation. Yours might be a bit different. With the examples above you should be able to modify them for your implementation. 

**Every component should have its own test file, we put everything in one here because it is easier to present. So as an example, with our implementation, there should be an `App.test.js`, a `DisplayResult.test.js` and a `MethodSelect.test.js` file in the `__tests__` folder** 

```js
import React from 'react';
import { mount, shallow } from 'enzyme';
import { stub } from 'sinon';

import DisplayResult from './DisplayResult'
import MethodSelect from './MethodSelect';
import App from './App';
```
Here we are importing everything that we need to test our application and what we want to test. 

The first three are what we need to be able to test. `enzyme` we need to be able to create fake versions of the components that we want to test. `react` we need because we are testing an application built with the React library. `sinon` we need to have to be able to mock and stub out some of the functions that we have in our components. 

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

So here we are testing the `App` component, as it states in the describe block. The first test here is to make sure that the `App` component renders the header. First, we create the component with a method from enzyme that we imported called shallow. 

*[Shallow rendering is useful to constrain yourself to testing a component as a unit, and to ensure that your tests aren't indirectly asserting on behavior of child components.](https://github.com/airbnb/enzyme/blob/master/docs/api/shallow.md)*
 
 After this, we declare a variable and makes it equal a bit of HTML code. Then in the expect block we test for that the HTML code that we stored in the variable named `header` exists in the `App` component. Next test is `'shows metric as the standard method'`. In our `App` component, we have a constructor where we set the calculation method as a sate and it equals to metric. This means that everytime someone opens the application, metric is going to be the default method. The way we have implemented our app is that the label for the inputs change depending on which method is selected. So if the labels are correct they should show "kg" and "cm" in the parenthesis. So as the test above we store what HTML code we expect to see in variables and then we test for them to be rendered by the `App` component. 

```js
it('can change method', () => {
  const onChangeValue = stub();
  const component = shallow(<App onChangeValue={onChangeValue} />);
  const weightLabel = <label>Weight(lbs)</label>;
  const heightLabel = <label>Height(inches)</label>;
  component.find("MethodSelect").prop('onChangeValue')({target: {value:'imperial'}});
  expect(component.contains(weightLabel)).toEqual(true);
  expect(component.contains(heightLabel)).toEqual(true);
})
```

Here we test that we can change the method to imperial and that the labels for the input change to reflect that. In our implementation we have the method selector in a different component then the `Àpp` one, we have it in a child component called `MethodSelect`. So every time we change the value of the method selector in the `MethodSelect` component, the `App` (parent) component notices this and grabs the value of which option (either imperial or metric) was selected and sets the state of the method to that value. 

We stub this `onChangeValue` out because we don't have access to the `MethodSelect` component in this test. We declare two variables with what we expect to see when the state of method has been changed to imperial. On the next line we set the onChangeValue we have stubbed out previously to imperial. This means that the state of the method has changed to "imperial" and we can now test that the labels have changed and match the variables we declared earlier.

```js
describe('<DisplayResult />', () => {
  it('displays the calulation correct(metric)', () => {
    const component = shallow(<DisplayResult method='metric' weight='100' height='195'/>)
    const response = <div id='response'>You are Overweight with a BMI of 26.3</div>
    expect(component.contains(response)).toEqual(true)
  })

  it('displays the calulation correct(imperial)', () => {
    const component = shallow(<DisplayResult method='imperial' weight='140' height='73'/>)
    const response = <div id='response'>You are Underweight with a BMI of 18.47</div>
    expect(component.contains(response)).toEqual(true)
  })

  it('does not show anything when one of the input fields are empty', () => {
    const component = shallow(<DisplayResult method='metric' weight='' height='195'/>);
    expect(component.text()).toBe('')
  })
})
```
In these test here we test that we get the correct response from the `DisplayResult` component. This component gets the method type and values from the input fields in the `App` component as props. Then it runs the calculation and returns the result. So above here, we have three different tests. That the metric and imperial calculations work properly. As well as that when we don't fill in both input fields, we don't get any response. 

So for the first two tests, we set up the `DisplayResult` component with the props that are needed to do the calculation. Then we declare a variable and set it to equal the response we want to get from the component when we pass in those props to it. The last test we don't send in all necessary props, which is what we want to test. Therefore in the expect block we test that the component doesn't render any text. 
```js
describe('<MethodSelect />', () => {
  it('has two methods to choose from', () => {
    const component = mount(<MethodSelect />);
    const selector = component.find('#method').instance()
    expect(selector.options.length).toEqual(2)
  }
)})
```

In this last test, we are making sure that there are two options of methods to choose from in the `MethodSelect` component. This component does not set the state of method, we do that in the `App` component. 

That means that this is really the only thing we need to test in this component. We first set up the `MethodSelect` component, but this time with a different enzyme method called [mount](https://airbnb.io/enzyme/docs/api/ReactWrapper/mount.html). This test is a bit different from the other tests in this guide. Previously we have set a variable and equaled it to some HTML, then we have checked to see if the component has rendered that HTML. In this test, we find the selector by its id within the component, and then test that the selector has two options to choose from. 

