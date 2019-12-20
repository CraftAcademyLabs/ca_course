Next feature that we want to implement is that the user can save their cooper result.

So let's add a feature file for this:

`$ touch cypress/integration/userCanSavePerformanceData.spec.js`

```js
// cypress/integration/userCanSavePerformanceData.spec.js

/// <reference types="Cypress" />

describe("User attempts save data", () => {
  beforeEach(() => {
    cy.visit("http://localhost:3001");
    cy.server();
    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/v1/auth/sign_in",
      response: "fixture:login.json",
      headers: {
        uid: "user@mail.com"
      }
    });
    cy.get("#login").click();
    cy.get("#login-form").within(() => {
      cy.get("#email").type("user@mail.com");
      cy.get("#password").type("password");
      cy.get("button").click();
    });
  });

  it("successfully", () => {
    cy.get("input#distance").type("1000");
    cy.get("select#gender").select("female");
    cy.get("input#age").type("23");
    cy.get("#save-result").click();
    cy.contains("Your entry was saved");
  });

  it("can save two different entries", () => {
    cy.get("input#distance").type("1000");
    cy.get("select#gender").select("female");
    cy.get("input#age").type("23");
    cy.get("#save-result").click();
    cy.contains("Your entry was saved");
    cy.get("input#distance")
      .clear()
      .type("1500");
    cy.get("#save-result").click();
    cy.contains("Your entry was saved");
  });
});
```

We want to be able to run these test without sending requests to the backend. Let's mock the response out.

We need to update our `beforeEach` block in our feature:

```js
// cypress/integration/userCanSavePerformanceData.spec.js

/// <reference types="Cypress" />

describe("User attempts save data", () => {
  beforeEach(() => {
    cy.visit("http://localhost:3001");
    cy.server();
    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/v1/auth/sign_in",
      response: "fixture:login.json",
      headers: {
        uid: "user@mail.com"
      }
    });
    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/v1/performance_data",
      response: { message: "all good" },
      headers: {
        uid: "user@mail.com"
      }
    });
    cy.get("#login").click();
    cy.get("#login-form").within(() => {
      cy.get("#email").type("user@mail.com");
      cy.get("#password").type("password");
      cy.get("button").click();
    });
  });
  // ...
});
```

Cypress is smart enough to be able to handle javascript objects as responses, so if the response is small we can just add it directly as above.

When a user is logged in and have a cooper result, he should be able to click a button to save that result.

Let's start with adding a conditional in our render method to show the `#save-result` button if the user is logged in and have a result. Add this to the render method in the `DisplayCooperResult` component:

```js
// src/components/DisplayCooperResult.jsx

import React from "react";

import coopercalculator from "../modules/cooperCalculator";
import saveData from "../modules/performanceData";

const DisplayCooperResult = ({
  distance,
  gender,
  age,
  authenticated,
  entrySaved,
  entryHandler
}) => {
  const result = coopercalculator(distance, gender, age);

  const propsPassed = distance && age ? true : false;

  return (
    <>
      {propsPassed && (
        <>
          <p id="cooper-message">
            {age} y/o {gender} running {distance} meters.
          </p>
          <p id="cooper-result">Result: {result}</p>
          {authenticated && !entrySaved ? (
            <button
              id="save-result"
              onClick={() => saveData(result, entryHandler)}
            >
              Save entry
            </button>
          ) : (
            <p>Your entry was saved</p>
          )}
        </>
      )}
    </>
  );
};

export default DisplayCooperResult;
```

If there is a result and the user is authenticated, then the `#save-result` button will show. The application will complain about `saveData()` not being defined. We need to add a new module to get this to work. Run:

`$ touch src/modules/performanceData.js`

Add this:

```js
// src/modules/performanceData.js

import axios from "axios";

const saveData = async (result, entryHandler) => {
  let headers = sessionStorage.getItem("credentials");
  headers = JSON.parse(headers);
  headers = {
    ...headers,
    "Content-type": "application/json",
    Accept: "application/json"
  };
  try {
    await axios.post(
      "/performance_data",
      {
        performance_data: { data: { message: result } }
      },
      {
        headers: headers
      }
    );
    entryHandler();
  } catch (err) {
    console.error(err);
    alert("Something went wrong");
  }
};

export default saveData;
```

You have to import this file to the `DisplayCooperResult` component.

`import saveData from "../modules/performanceData";`

So from our `DisplayCooperResult` component, we send in the result to this function. The first thing we do is grabbing the credentials we have stored in `sessionStorage`. Then we make a post request to the backend with those credentials and the cooper result. The response we get back gets sent back to saveCooperData function in the `DisplayCooperResult` component. We also call on the function `storeAuthHeaders` we have in the `auth` module to update the credentials that we get in the response. If there is no error in the response, we call on the entryHandler. We have not defined the `entryHandler` yet, but let's do it now.

Add this to `App` component:

We also have to set that new state in our constructor:

```js
// src/App.jsx

state = {
  // ...
  entrySaved: false
};
```

Now we need to pass the entryHandler and `entrySaved` state to the `DisplayCooperResult` component:

```js
<DisplayCooperResult
  distance={this.state.distance}
  gender={this.state.gender}
  age={this.state.age}
  authenticated={this.state.authenticated}
  entrySaved={this.state.entrySaved}
  entryHandler={() => this.setState({ entrySaved: true })}
/>
```

If you run the feature test now, you will get an error that says `"Text not found "Your entry was saved"`. That's not weird, we haven't rendered any message when we get a successful response. If you take a look at logs for the backend, you will see that the performance data is being saved correctly. You can see the logs in the terminal where you started up the rails server.

We need to add and modify some of our code to get it to go green.

First we need to modify the if statement in `DisplayCooperResult` component:

```js
// src/components/DisplayCooperResult.jsx

import React from "react";

import coopercalculator from "../modules/cooperCalculator";
import saveData from "../modules/performanceData";

const DisplayCooperResult = ({
  distance,
  gender,
  age,
  authenticated,
  entrySaved,
  entryHandler
}) => {
  const result = coopercalculator(distance, gender, age);

  const propsPassed = distance && age ? true : false;

  return (
    <>
      {propsPassed && (
        <>
          <p id="cooper-message">
            {age} y/o {gender} running {distance} meters.
          </p>
          <p id="cooper-result">Result: {result}</p>
          {authenticated && !entrySaved ? (
            <button
              id="save-result"
              onClick={() => saveData(result, entryHandler)}
            >
              Save entry
            </button>
          ) : (
            <p>Your entry was saved</p>
          )}
        </>
      )}
    </>
  );
};

export default DisplayCooperResult;
```

Previously we modified the rendering of this component in the `App` component to pass in the state of `entrySaved`. So if we have saved the result we want to see `"Your entry was saved"`. Now we need to modify the `onChange` function in the app component so that every time we modify the inputs we can save a new result.

```js
// src/App.jsx

...
onChangeHandler = e => {
  this.setState({ [e.target.name]: e.target.value, entrySaved: false });
};
```

If you run the test now, both test in the feature file should go green. If not, go over the code again.
