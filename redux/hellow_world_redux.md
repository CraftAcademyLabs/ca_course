# The worlds most complicated Hello World application

What is redux
Redux is a library that helps us with state management. React is perfectly capable to handle state management as it is. However when we are passing props and state left and right. This could potentially become very hard for us to manage as our application grows in complexity.

Redux is a separate library from React which helps us with just that. Reducing complexity.

That being said. Redux itself could potentially be confusing. But the complexity drops a lot as soon as you understand the basics and the different moving part of redox.

So let's dive in.

## What does Redux do with state?

What redux does is that it extract the state from the application and stores it. And the components that need to access the state subscribe and as soon as a state changes the components get rerenders.

There are three moving parts of Redux

- Actions
- Reducers
- Store

### Actions

"Actions" are used to send information from the application to the "Store"

So we send an action to the store when we need to change the state. The state is changed when the user interacts with the application or when we need to do API calls etc.

So actions send information from the application to the store

So what are actions? Well, the are javascript objects. They need two properties.

- Type (Mandatory)
- Payload (Holds the information we want to pass)

An example of a action:

```js
{
type: LOGIN_USER,
payload: {email: 'email@email.com', password: 'supersecretpassword'}
}
```

To create an action we use "Action Creators", these are javascript functions

An example of an action creator:

```js
const authenticateUser = credentials => {
  return {
    type: AUTHENTICATE_USER,
    payload: data
  };
};
```

In order to invoke the action we use something called "dispatch" function.

```js
dispatch(authenticateUser(credentials));
```

### Reducers

Reducers are JS functions that modify the state. They take the current application state looks at it and returns a new state.

We only use reducers to modify the state. We never modify the state directly. Actions only describe what happened, but don't describe how the application's state changes. For this we use reducers.

### Store

The Store is the object that brings Actions and Reducers together. The store has the following responsibilities including:

- Holds application state;
- Allows access to state via getState();
- Allows the state to be updated via dispatch(action);

Let see all of this in action to understand it better.

## Code walkthrough

In this section, we are going to build a new Hello world application where we are slowly going to introduce redux step by step. Usually, we would never use redux for a simple hello world application. It's like using a sledgehammer to drive a nail.

But for us to understand the moving parts and the concepts we are going to just that.

Create a new react app by running

```bash
$ yarn create react-app hello_world_redux
```

Open up the app and clean out all the scaffolded code.

Delete and remove the imports of the following files:
App.css
index.css
logo.svg
setupTest.js

Rename the App.js to App.jsx and add the following code,

```js
import React from "react";

const App = () => {
  return (
    <>
      <h1>Hello World</h1>
    </>
  );
};

export default App;
```

Open your server and we have the firsts version of the hello world application. Commit and save and let us add some more functionality

Let's refactor this to a class-based component.

```js
import React, { Component } from "react";

class App extends Component {
  render() {
    return (
      <div>
        <h1>Hello world from a class component</h1>
      </div>
    );
  }
}
export default App;
```

Run the code, make sure it works. And make a commit.
The reason we changed to a class-based component was that we could use state.
So let's add a state with the greeting message, and display the message instead of the hardcoded greeting.

```js
import React, { Component } from "react";

class App extends Component {
  state = {
    message: "Hello world from the component state"
  };

  render() {
    return (
      <div>
        <h1>{this.state.message}</h1>
      </div>
    );
  }
}
export default App;
```

Run the code, make sure that you are seeing the "Hello world from component state" message in your browser. And rejoice and celebrate with a commit.

Time to add Redux

## Adding and using Redux in your application

Let' start by adding the packages we need

```bash
$ yarn add redux react-redux
```

We need to add both redux and another library called react-redux. React-redux makes a connection to our react application so that we can use redux. Remember that redux is not a part of react even though it's very popular in the react community. You could use redux with other frameworks and libraries such as Angular and Vue.

The next step is to create a few files and folders that we will need.

```bash
$ mkdir src/state
$ mkdir src/state/reducers
$ mkdir src/state/store
$ touch src/state/reducers/rootReducer.js
$ touch src/state/store/configureStore.js
$ touch src/state/store/initialState.js
```

Application structure will look like this now

```bash
src/
├── App.jsxp
├── index.js
├── serviceWorker.js
└── state
    ├── reducers
    │   └── rootReducer.js
    └── store
        ├── configureStore.js
        └── initialState.js
```

Open your `initialState.js` file and add

```js
const initialState = {
  message: "Hello World from redux"
};

export default initialState;
```

We are setting the message here that we will like to be displayed in the browser later on.

The next step is to create a reducer, head over to your rootReducer and add the following code.

```js
import initialState from "../store/initialState";

const rootReducer = (state = initalState) => {
  return state;
};

export default rootReducer;
```

We will revisit this file in a moment, but for now, all we are doing is to return the state object.

next step is to add the following content to the configureStore.js

```js
import { createStore } from "redux";
import rootReducer from "../reducers/rootReducer";

const configureStore = () => {
  return createStore(rootReducer);
};

export default configureStore;
```

We have our basic setup done we have added the basic components that we need and now we need to connect our application to the store.

Head over to your index.js, we need to import a component called provider from react-redux and we also need to import the configureStore to the index.js

Then we need to wrap the App component with the provider and pass in the store as a property.

```js
import { Provider } from "react-redux";
import configureStore from "./state/store/configureStore";

const store = configureStore();

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

If you look at your browser now, we still see no changes. But the object is there. Let's deviate from our path a little and go on to see the object with the message in our developer tools.

add `window.store = store` in your index.js file.

```js
[...]
const store = configureStore();
window.store = store

ReactDOM.render(
[...]
```

Now head over to the browser and go into the console and type the following
`window.store.getState()`, you will now see the message "Hello World from redux". Great! Commit!

Time to display the message.

## Connecting to redux

Head over to your App.jsx and import

```js
import { connect } from "react-redux";
```

And add the connect to the export

```js
export default connect()(App);
```

We now need to make sure that our component gets the state in the form of props.

Add the following function to your App component, however, this function needs to be outside the class definition for us to use it, as well as that we need to pass it in as an argument to the connect function.

```js
const mapStateToProps = state => {
  return {
    state: state
  };
};

export default connect(mapStateToProps)(App);
```

And finally, update the return statement in your class component to display the content of the state:

```js
    return (
      <div>
        <h1>{this.props.state.message}</h1>
      </div>
```

Go over to your browser and then you should see the message "Hello world from redux".

I think a small recap would be in order. What have we done in this guide?

We have set up the basic configuration for redux
We connected the state to the react application
and we connected our component to the application so we could use it.

We will expand the functionality in the next section.

## Adding user interaction

We have added redux to our hello world application. In our App.jsx component we are no longer setting the state inside the component. All that functionality has been moved to redux. This means that there is no longer any need for a class-based component. So to be consistent we will refactor this component to a functional one.

`App.jsx`

```js
import React from "react";
import { connect } from "react-redux";
const App = props => {
  return (
    <div>
      <h1>{props.message}</h1>
    </div>
  );
};
const mapStateToProps = state => {
  return {
    message: state.message
  };
};

export default connect(mapStateToProps)(App);
```

Start your server and make sure it's working, and commit if it is.

So what are we going to do in this section? Well, a nice idea would be to have a greeting that is passed by the user. With an input field where the user can write a message that will be displayed on the page.

Let's do that!

Start by modifying your rootReducer.js to take the action as the argument and to use the payload to display the message.

```js
import initialState from "../store/initialState";

const rootReducer = (state = initialState, action) => {
  return {
    ...state,
    message: action.payload || initialState.message
  };
};

export default rootReducer;
```

Next step is to add a button that will change the message and by doing so also change the state of the application.

Refactor your App.jsx return statement to this:

```js
  return (
    <>
      <h1>{props.message}</h1>
      <button onClick={() => {
        props.dispatch({type: '', payload: 'Hello from button'})
      }
      }>Change message</button>
    </>
```

So what are we doing here? We are taking a button and making it change the message. We can have this function directly inside the onClick, or create a separate function that calls on it. This functionality is not quite what we want. But we are on our way.

The next step is to make the message to be user-generated. We need to extract the onClick event to a separate function to make the code more readable, add an input field and save the data from the input field and dispatch it to our reducer.

```js
const App = props => {
  const changeMessage = () => {
    let inputField = document.querySelector('input[name="new-message"]');
    props.dispatch({ type: "", payload: inputField.value });
  };

  return (
    <>
      <h1>{props.message}</h1>
      <input
        type="text"
        name="new-message"
        placeholder="type your message here"
      />
      <button onClick={changeMessage}>Change message</button>
    </>
  );
};
```

Try it out and make sure that you have no typos. If it works then commit and send it off to github.

Alright so far so good, the user can change the message by writing it to the input field. Cool. Let´s experiment a little and add some more functionality.

Add the following in your App.jsx

```js
return (
  <>
    <h1>{props.message}</h1>
    <input
      type="text"
      name="new-message"
      placeholder="type your message here"
      onBlur={event => {
        props.dispatch({
          type: "PREPARE_GREETING",
          payload: event.target.value
        });
      }}
    />
    <button onClick={changeMessage}>Change message</button>
  </>
);
```

And add the type `'CHANGE_GREETING'` to the `changeMessage` function
Go to your browser and try it out, what is happening? Well we are using the onBlur effect to change the greeting. Cool huh?

Cool, but it has a problem. Now we have two sets of functionality that does the same thing. Which is not what we want. Let's make some changes to our reducer.

```js
const rootReducer = (state = initialState, action) => {
  if (action.type === "PREPARE_GREETING") {
    return {
      ...state,
      new_message: action.payload
    };
  } else if (action.type === "CHANGE_GREETING") {
    return {
      ...state,
      message: state.new_message
    };
  } else {
    return state;
  }
};

export default rootReducer;
```

And now we don't need the value from the input field in the changeGreeting function.

```js
const changeMessage = () => {
  props.dispatch({ type: "CHANGE_GREETING" });
};
```

Make sure it works and commit the changes.

Let's do some refactoring to the code. To have better code readability to our reducer we want to add a switch statement instead.

```js
const rootReducer = (state = initialState, action) => {
  switch (action.type) {
    case "PREPARE_GREETING":
      return {
        ...state,
        new_message: action.payload
      };
    case "CHANGE_GREETING":
      return {
        ...state,
        message: state.new_message
      };
    default:
      return state;
  }
};

export default rootReducer;
```

Make sure the code still works. The last step is that we would like to extract the input field to a separate component.

```js
import React from "react";
import { connect } from "react-redux";

const ChangeGreetingInput = props => {
  return (
    <>
      <input
        type="text"
        name="new-message"
        placeholder="type your message here"
        onBlur={event => {
          props.dispatch({
            type: "PREPARE_GREETING",
            payload: event.target.value
          });
        }}
      />
    </>
  );
};

export default connect()(ChangeGreetingInput);
```

And in the App.jsx

```js
return (
  <>
    <h1>{props.message}</h1>
    <ChangeGreetingInput />
    <button onClick={changeMessage}>Change message</button>
  </>
);
```

Make sure that you notice that we need to add in the connect in all components that we need to connect to redux.

We have now completed the worlds most complex hello world application using react
