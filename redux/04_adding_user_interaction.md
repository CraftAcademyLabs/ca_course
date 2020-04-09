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
