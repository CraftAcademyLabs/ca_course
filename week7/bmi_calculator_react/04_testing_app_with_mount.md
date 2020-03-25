## Test components with mount

Now that we have tested our components with shallow, we can also test the `App` component and see if our logic works as it should. This is an example your test might be different based on how you have implemented the imperial method. In our example we have a selector to choose between the methods.

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
