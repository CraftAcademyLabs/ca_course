## Component testing

Now we are going to do some unit testing, or as we call them in React land, Component tests! We are going to use `jest`, `enzyme` to do this. So as usual, we need to add some packages. Run:

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

Now we need to create our test folder and our first test file. Run:

## Testing components with shallow rendering

First we are going to test our form and message components. For this we will use shallow render and mock out the functions that we receive from `App.js`:

```
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

To run the test simply type in :

```
yarn test

```

Now let's test the `Message` component!:

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

Let me walk you through what is happening here:

- First we `mount` the `App` component, meaning all the children components are mounted as well. Therefore we have access to `Form` and `Message`.
- First we find the selector and select the metric method using the `simulate('change')` function. We can pass a secondary argument where we construct the event we want to happen. We choose metric.
- Secondary we find the 2 inputs and simulate changes. Now we also need to pass down the name of the input because that is how we are setting the state with our `onChangeHandler`.
- After that we simulate the submit and find the expected message.
- We repeat with the imperial method.


## Wrap up

This is how you test the components. The primary goal of these tests modules of your program individually and to make sure that the logic of your program works as intended. As your application grows it will be harder to keep track of what change affects what. Having good component specs will help you see the effects of those changes on every component.