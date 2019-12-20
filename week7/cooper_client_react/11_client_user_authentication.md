# Authentication

It is time to add some login functionality to our React application. In order to be able to save results and show past results, we need to have a registered user.

We are going to start as usual with writing a feature test for this functionality. We start with creating a feature file and adding some basic tests (aka scenarios).

`$ touch cypress/integration/userCanLogin.spec.js`

This feature file should look like this:

```js
// cypress/integration/userCanLogin.spec.js

/// <reference types="Cypress" />

describe("User can log in", () => {
  beforeEach(() => {
    cy.visit("/");
  });
  it("successfully", () => {
    cy.get("#login").click();
    cy.get("#login-form").within(() => {
      cy.get("#email").type("user@mail.com");
      cy.get("#password").type("password");
      cy.get("button").click();
    });
    cy.contains("Hi user@mail.com");
  });

  it("with invalid credentials", () => {
    cy.get("#login").click();
    cy.get("#login-form").within(() => {
      cy.get("#email").type("user@mail.com");
      cy.get("#password").type("wrongpassword");
      cy.get("button").click();
    });
    cy.contains("Invalid login credentials. Please try again.");
  });
});
```

This feature is pretty straight forward. We click a login button which renders a login form. In the first scenario, we fill in the correct credentials and in the second one we fill in the wrong ones. Depending on if it is successful or not we will get a response which either welcomes the user or returns an error message.

If we run the test now we get an error that states that the test can't find the selector `#login`. Let's start with adding a button to our `App` component.

```js
// src/App.js

...

  render() {
    return (
      <>
        <InputFields onChangeHandler={this.onChangeHandler} />
        <button id="login">Login</button>
        <DisplayCooperResult
          distance={this.state.distance}
          gender={this.state.gender}
          age={this.state.age}
        />
      </>
    );
  }
}
```

That takes care of the first error. Now, we have a different error that is very similar to the previous one. It can't find the input field for filling in the email.

At this point, we want to create a new component for the login form. This component should have input fields for the credentials and a button to submit them.

Let's create this login form component:

`$ touch src/components/LoginForm.jsx`

Add the following code to that file:

```js
// src/components/LoginForm.jsx

import React from "react";

const LoginForm = props => {
  return (
    <form id="login-form">
      <label>Email</label>
      <input name="email" type="email" id="email"></input>

      <label>Password</label>
      <input name="password" type="password" id="password"></input>

      <button id="submit">Submit</button>
    </form>
  );
};

export default LoginForm;
```

If we run the test again, we will see no difference in the output. That is because we don't use this component anywhere, we are not rendering it. In the app component, we need to import it and render it.

Modify `App.js` with the following code.

```js
import React, { Component } from "react";
import "./App.css";
import DisplayCooperResult from "./DisplayCooperResult";
import LoginForm from "./Components/LoginForm";

class App extends Component {
  // ...

  render() {
    return (
      <>
        {/* ... */}
        <button id="login">Login</button>
        <LoginForm />
      </>
    );
  }
}

export default App;
```

If you run the feature test now, it will complain about not finding the text that we are expecting to find. So at the moment, the test can find all of the elements it needs to fulfill its task. The flow that we want is that when the user clicks the login button we want the login form to render. At the moment we have a button that does nothing. If you run the application you can see that.

We want to add an object to our state to determine if the LoginForm should be rendered or if we want to display the login button. Then, we want to condition what is being rendered (in the components `render` method), based on that state.

We need to update the App component to look like this:

```js
import React, { Component } from "react";

import DisplayCooperResult from "./components/DisplayCooperResult";
import InputFields from "./components/InputFields";
import LoginForm from "./components/LoginForm";

class App extends Component {
  state = {
    distance: "",
    gender: "female",
    age: "",
    renderLoginForm: false
  };

  onChangeHandler = e => {
    this.setState({ [e.target.name]: e.target.value });
  };

  render() {
    const renderLogin = this.state.renderLoginForm ? (
      <LoginForm />
    ) : (
      <button
        id="login"
        onClick={() => this.setState({ renderLoginForm: true })}
      >
        Login
      </button>
    );
    return (
      <>
        <InputFields onChangeHandler={this.onChangeHandler} />
        {renderLogin}
        <DisplayCooperResult
          distance={this.state.distance}
          gender={this.state.gender}
          age={this.state.age}
        />
      </>
    );
  }
}

export default App;
```

So we have added a state called `renderLoginForm` in the constructor. That state is going to decide if the application should render the login button or the login form.

At the beginning of the render method, we declare a variable called `renderLogin`. Depending on whether the `renderLoginForm` state is equal to true or false, this variable will contain the login form or the login button. So if the `renderLoginForm` state is true it will set the variable to render the login form, if it's false, it will render the login button.

We have added an `onClick` to the login button. If the user press it, it will switch the `renderLoginForm` state to true. Since we are changing state using the `setState()` function, that also means that the component will be re-rendered and login form will show up on the page. This variable (`renderLogin`) where we store all of this, needs to be added to the return in the render method, we have placed it underneath the `DisplayCooperResult` component.

Run the application to make sure that this works and that the visibility of the login form is toggled.

If you run the feature test again, you will see that the change we made has not changed the error message from the one we got before. We need to add some logic to our component in order to make the login flow work.

We are going to add a method called `onLogin` that is going to authenticate the user against the backend system.

First, you need to add this method to the App component:

```js
// src/App.jsx

...
onLogin = async e => {
    e.preventDefault();
    const res = await authenticate(
      e.target.email.value,
      e.target.password.value
    );
    if (res.authenticated) {
      this.setState({ authenticated: true });
    } else {
      this.setState({ message: res.message, renderLoginForm: false });
    }
  };


```

You can see that we use another way of passing data from a form. We are using the event object to get the values of the input fields inside our form. However we use new attributes in state so let's add them!

```js
// src/App.jsx

...
state = {
    distance: "",
    gender: "female",
    age: "",
    renderLoginForm: false,
    authenticated: false,
    message: ""
  };
```

We also need to add some props to the rendering of the `LoginFrom` component.

```js
<LoginForm
  submitFormHandler={this.onLogin}
/>
```

What we do here is that every time we change the input fields in the `LoginForm` component we will update the state of email and password. We also add so that when the submit button is clicked (that is when `onSubmit` is called for forms by default), we run the `onLogin` method here in the `App` component. To make this work we need to make some changes in the `LoginForm` component.'

```js
import React from "react";

const LoginForm = ({ submitFormHandler }) => {
  return (
    <form onSubmit={submitFormHandler} id="login-form">
      <label>Email</label>
      <input
        name="email"
        type="email"
        id="email"
      ></input>

      <label>Password</label>
      <input
        name="password"
        type="password"
        id="password"
      ></input>
      <button id="submit">Submit</button>
    </form>
  );
};

export default LoginForm;
```

If you run the test now, all of them will fail. The app complains about how `authenticate` in the `App` component is not defined.

Like we did with the `CooperCalculator`, we will extract the login function to a separate module. That is what we are trying to call on with `authenticate` in the `onLogin` function.

We will make a call to our backend API over the network and will need to add a library to be able to perform that call. The library we will use is [Axios](https://github.com/axios/axios). You install it like this:

`$ yarn add axios`

And configure axios a little in our `index.js`:

```js
// src/index.js

import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import * as serviceWorker from "./serviceWorker";
import axios from "axios";

axios.defaults.baseURL = "http://localhost:3000/api/v1";

ReactDOM.render(<App />, document.getElementById("root"));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();
```

This will set the baseUrl for all of our axios network calls.

Let's add the module that will handle them:

`$ touch src/modules/auth.js`


```js
import axios from "axios";

const authenticate = async (email, password) => {
  try {
    const response = await axios.post("/auth/sign_in", {
      email: email,
      password: password
    });
    await storeAuthCredentials(response);
    return { authenticated: true };
  } catch (error) {
    return { authenticated: false, message: error.response.data.errors[0] };
  }
};

const storeAuthCredentials = ({ headers }) => {
  sessionStorage.setItem("uid", headers["uid"]);
  sessionStorage.setItem("client", headers["client"]);
  sessionStorage.setItem("access_token", headers["access-token"]);
  sessionStorage.setItem("expiry", headers["expiry"]);
  sessionStorage.setItem("token_type", "Bearer");
};

export default authenticate;
```

We also need to import this to the `App` component:

`import { authenticate } from './Modules/Auth';`

In the backend, make sure that you have a user that has `user@mail.com` as the email and `password` as the password.

If you run the test and have the backend running locally, you will see that we still get the same error message about how it can't find the text that we are expecting to see. But, we got rid of the error message that we got when we ran the application about how `authenticate` is not defined. If you take a look at the terminal window where you run the backend, you can actually see that we hit it two times, one successful and one not successful. Now it just a question of fetching the response and display it to the user.

So, if we login successfully and store the user in the `sessionStorage`, we want to to display `Hi user@mail.com`. On the other hand, if the login is not successful, we send back the message we get from the response back to the `onLogin` function and store it in a state called message. We want to display that message our page.

Modify the code in the `render` method for the `App` component to look like this:

```js
 render() {
        const { renderLoginForm, authenticated, message } = this.state;
    let renderLogin;
    if (renderLoginForm && !authenticated) {
      renderLogin = (
        <LoginForm
          submitFormHandler={this.onLogin}
        />
      );
    }
    if (!renderLoginForm && !authenticated) {
      renderLogin = (
        <>
          <button
            id="login"
            onClick={() => this.setState({ renderLoginForm: true })}
          >
            Login
          </button>
          <p>{message}</p>
        </>
      );
    }
    if (authenticated) {
      renderLogin = (
        <p>Hi {(sessionStorage.getItem("uid"))}</p>
      );
    }

    return (
      // <...>
    );
  }
```

If you run the tests now with the backend running, everything should go green! If not make sure that you go through the code again, run the application and try it out manually to see at what point the application is running in to errors.
