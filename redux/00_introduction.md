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

