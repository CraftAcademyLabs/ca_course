So now it is time to add some login functionality to our application. In order to be able to save results and show past results, we need to have a registered user.

We are going to start as usual with a feature test for this.

`touch  src/__features__/UserCanLogin.feature.js`

This feature file should look like this:

```js
describe('User attempts to login', () => {

  beforeAll(async () => {
    jest.setTimeout(10000)
    await page.goto(appURL);

  });

  beforeEach(async () => {
    await page.reload();
  })


  it('with valid credentials', async () => {
    await page.click('#login')
    await page.type('input[id="email"]', 'johndoe@mail.com')
    await page.type('input[id="password"]', 'password')
    await page.click('button[id="submit"]')
    await expect(page).toMatch('Hi johndoe@mail.com')
  })

  it('with invalid credentials', async () => {
    await page.click('#login')
    await page.type('input[id="email"]', 'wrongjohndoe@mail.com')
    await page.type('input[id="password"]', 'wronpassword')
    await page.click('button[id="submit"]')
    await expect(page).toMatch('Invalid login credentials. Please try again.')
  })
})

```

This feature is pretty straight forward. We click a login button which renders a login form. In the first scenario, we fill in the correct credentials and in the second one we fill in the wrong ones. Depending on if it is successful or not we will get a response wich either welcomes the user or returns an error message.

If run the test now we get an error that states that the test can't find the selector `#login`. So let's start with adding that button to our App component.

```js
// src/App.js

...

 render() {
    return (
      <div className="App">
        <div>
          <label>Distance</label>
          <input id="distance" onChange={this.onChange.bind(this)}></input>
        </div>

        <select id="gender" onChange={this.onChange.bind(this)}>
          <option value="female">Female</option>
          <option value="male">Male</option>
        </select>

        <div>
          <label>Age</label>
          <input id="age" onChange={this.onChange.bind(this)}></input>
        </div>

        <button id="login">Login</button>

        <DisplayCooperResult
          distance={this.state.distance}
          gender={this.state.gender}
          age={this.state.age}
        />
      </div>
    );
  }
}
```
So that takes care of that error. Now we have a different error that is very similiar to the previous one. It cant find the input field for filling in the email.
At this point, we want to create a new component for the login form. So what we want is a button that renders the login form. Then when we fill in the credentials the application should either greet the user or give an error message.

Let's start with adding this Login form component.
`mkdir src/Components`
`touch src/Components/LoginForm.js`

Aadd this to that file:

```js
import React, { Component } from 'react';

class LoginForm extends Component {
  render() {
    return (
      <form>
        <div>
          <label >Email</label>
          <input id="email"></input>
        </div>

        <div>
          <label>Password</label>
          <input id="password"></input>
        </div>

        <button id="submit">Submit</button>

      </form>
    )
  }
}

export default LoginForm;

```

If we run the test again, we will see no difference in the output. That is because we don't use this component anywhere, we don't render it.
In the app component, we need to import it and render it.

Modify `App.js` with the following code.

```js
import React, { Component } from 'react';
import './App.css';
import DisplayCooperResult from './DisplayCooperResult'
import LoginForm from './Components/LoginForm';


class App extends Component {
  ...

 render() {
    return (
      <div>
      ...

        <button id="login">Login</button>
        <LoginForm />
      </div>
    );
  }
}

export default App;
```

If you run the feature test now, it will complain about not finding the text that we are expecting to find.
So at the moment, the test can find all of the elements it needs to fulfill its task. The flow that we want is that when we press the login button we want the login form, at the moment we have a button that does nothing. If you run the application you can see that.

To fix this you need to update the App component to look like this:
```js
import React, { Component } from 'react';
import './App.css';
import DisplayCooperResult from './DisplayCooperResult';
import LoginForm from './Components/LoginForm';


class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      distance: '',
      gender: 'female',
      age: '',
      renderLoginForm: false
    }
  }

  onChange(event) {
    this.setState({
      [event.target.id]: event.target.value
    });
  }

  render() {
    let renderLogin

    if (this.state.renderLoginForm === true) {
      renderLogin = (
        <>
          <LoginForm />
        </>
      )
    } else {
      renderLogin = (
        <button id="login" onClick={() => this.setState({ renderLoginForm: true })}>Login</button>
      )
    }
    return (
      <div className="App">
        <div>
          <label>Distance</label>
          <input id="distance" onChange={this.onChange.bind(this)}></input>
        </div>

        <select id="gender" onChange={this.onChange.bind(this)}>
          <option value="female">Female</option>
          <option value="male">Male</option>
        </select>

        <div>
          <label>Age</label>
          <input id="age" onChange={this.onChange.bind(this)}></input>
        </div>

        <DisplayCooperResult
          distance={this.state.distance}
          gender={this.state.gender}
          age={this.state.age}
        />
        {renderLogin}

      </div>
    );
  }
}

export default App;
```

So we have added a state called `renderLoginForm` in the constructor. That state is going to decide if the application should render the login button or the login form.

At the beginning of the render method, we declare a variable called `renderLogin`. Depending on whether the `renderLoginForm` state is equal to true or false, this variable will contain the login form or the login button. So if the `renderLoginForm` state is true it will set the variable to render the login form, if it's false, it will render the login button.

We have added an `onClick` to the login button. If the user press it, it will switch the `renderLoginForm` state to true. That also means that the login form will render. This variable where we store all of this, need to be added to the return in the render method, we have placed it underneath the `DisplayCooperResult` component.

Run the application to make sure that this works for you.

If you run the test again, you will see that this has not changed the error message we got before. We need to add some logic to make this actually work. 

We are going to add a method called `onLogin` that is going to authenticate the user against the backend. 

First, you need to add this method to the App component:

```js
  async onLogin(e) {
    e.preventDefault();
    let resp = await authenticate(this.state.email, this.state.password)
    if (resp.authenticated === true) {
      this.setState({ authenticated: true });
    } else {
      this.setState({ message: resp.message, renderLoginForm: false })
    }
  }
```
As you can see we have added some more states, we need to add them to the constructor.

```js
  constructor(props) {
    super(props);
    this.state = {
      distance: '',
      gender: 'female',
      age: '',
      renderLoginForm: false,
      authenticated: false,
      email: '',
      password: '',
      message: ''
    }
  }
```
We also need to add some props to the rendering of the `LoginFrom` component.

```js
<>
  <LoginForm
    loginHandler={this.onLogin.bind(this)}
    inputChangeHandler={this.onChange.bind(this)}
  />
</>
```
What we do here is that every time we change the input fields in the `LoginForm` component we will update the state of email and password. We also add so that when the submit button is clicked, we run the `onLogin` method here in the `App` component. To make this work e need to make some changes in the `LoginForm` component.'

```js
import React, { Component } from 'react';

class LoginForm extends Component {
  render() {
    return (
      <form>
        <div>
          <label >Email</label>
          <input id="email" onChange={this.props.inputChangeHandler}></input>
        </div>

        <div>
          <label>Password</label>
          <input id="password" onChange={this.props.inputChangeHandler}></input>
        </div>
        <button onClick={(e) => this.props.loginHandler(e)} id="submit">Submit</button>
      </form>
    )
  }
}

export default LoginForm;
```
If you run the test now, all of them will fail. The app complains about how `authenticate` in the `App` component is not defined. 
Like we did with the `CooperCalculator`, we will extract the login function to a separate module. That is what we are trying to call on with `authenticate` in the `onLogin` function. Let's add that module

`touch src/Modules/Auth.js`

Add this there:

```js
import axios from 'axios'

const apiUrl = 'http://localhost:3000/api/v1';

const authenticate = async (email, password) => {
    const path = apiUrl + '/auth/sign_in';
    try {
        let response = await axios.post(path, { email: email, password: password })
        sessionStorage.setItem('current_user', JSON.stringify({id: response.data.data.id}));
        await storeAuthHeaders(response)
        return {authenticated: true}
    } catch (error) {
        return {authenticated: false, message: error.response.data.errors[0]}
    }
};

const storeAuthHeaders = ({ headers }) => {
  return new Promise((resolve) => {
      const uid = headers['uid'],
          client = headers['client'],
          accessToken = headers['access-token'],
          expiry = headers['expiry'];

      sessionStorage.setItem('credentials', JSON.stringify({
          uid: uid,
          client: client,
          access_token: accessToken,
          expiry: expiry,
          token_type: 'Bearer'
      }));

      resolve(true)
  })
};

export { apiUrl, authenticate, storeAuthHeaders }

```

You will also need to add the axios package with npm, run `npm i axios -S`

We also need to import this to the `App` component:

```js
import { authenticate } from './Modules/Auth';

```

In the backend, make sure that you have a user that has `johndoe@mail.com` as the email and `password` as the password.

If you run the test and have the backend running locally, you will see that we still get the same error message about how it can't find the text that we are expecting to see. But, we got rid of the error message that we got when ran the application about how `authenticate` is not defined. If you take a look at the terminal window where you run the backend, you can actually see that we hit it two times, one successful and one not successful. Now it just a question of fetching the response and display it to the user.

So when we login successfully we store the user in the sessionStorage, we want to use that to display `Hi johndoe@mail.com`. If the login is not successful we send back the message we get from the response back to the `onLogin` function and store it in a state called message, we need to display that message. Modify the code in the render method for the `App` component to look like this:

```js
render() {
    let renderLogin;
    let user;

    if (this.state.authenticated === true) {
      user = JSON.parse(sessionStorage.getItem('credentials')).uid;
      renderLogin = (
        <p>Hi {user}</p>
      )
    } else {
      if (this.state.renderLoginForm === true) {
        renderLogin = (
          <>
            <LoginForm 
              loginHandler={this.onLogin.bind(this)}
              inputChangeHandler={this.onChange.bind(this)}
            />
          </>
        )
      } else {
        renderLogin = (
          <>
            <button id="login" onClick={() => this.setState({ renderLoginForm: true })}>Login</button>
            <p>{this.state.message}</p>
          </>
        )
      }
    }
    
    return (
      // <...>
    );
  }

```
If you run the tests now, everything should go green!

