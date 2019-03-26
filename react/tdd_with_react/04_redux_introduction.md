## Dependencies
The packages we will need are `redux` , `react-redux`, as well as `redux-thunk`. 

`redux-thunk` is necessary to return anything but plain objects from actions. We are going to request data from an api, and fetch returns a promise.

As usual install these using `yarn` or `npm` 

```bash 
$ yarn add NAME_OF_PACKAGE 
// or 
$ npm i -S NAME_OF_PACKAGE 
```
Your `package.json` file should now have been updated with these dependencies.

## Organizing Code
Just to get us started we will get the ball rolling with a little bit of setup. 

First, you will want three directories, `./actions` , `./reducers` , `./store`. Place all of these in the `src/modules` folder of your app (if you don't have the `modules` folder, please create it.). 

Create a `./store/configureStore.js` file. We will come back to this file in a moment.

You will also need to create a `./reducers/rootReducer.js` file. All various reducers you add will all be combined in the `rootReducer.js`.

In this app our only data to display will be called `users`, and for simplicity the reducer we will add can be called `./reducers/usersReducer.js`. Pluralization here is not significant other than for readability and clarity.


Now is also a good time to add our `./store/initialState.js` file, which will help us to ensure we always have some level of certainty about the shape of the application level state.

```javascript
// I will add a default user ti the initial state,
// feel free to remove this once you have successfully 
// fetched users from the api
export default {
  users: [{
    id: 1,
    first_name: 'Thomas',
    last_name: 'Ochman',
    avatar: 'https://pbs.twimg.com/profile_images/3097853083/7cf8f53920ad1c1dc30ac1f330daf947_400x400.jpeg'
  }]
}
```

In the `./actions` add a `./actions/actionTypes.js` file. This will help by having only one file in which to refer to a list of all the possible actions for the app. 

Also, we will need to add a `./actions/userActions.js` for the actions themselves.

## Adding Actions and Reducers
Actions describe something happening, and the Reducers responds to the action.

Starting with the `actionsTypes.js` here we just want to export the constants we plan on using for each action. In this case, let us start with just two simple actions, `FETCH_USES` and `RECEIVE_USERS`.

```javascript
// actionTypes.js
export const FETCH_USERS = 'FETCH_USERS';
export const RECEIVE_USES = 'RECEIVE_USERS';
```

We will also need the actions themselves. We are going to set this up with a function for each action type, which returns an object containing at least the type of the action.

In the case of fetching our users, we want to be sure we are getting our data, and so we request it by executing a fetch for our given URL. We will be using `axios` to make a call to an API, but you CAN use the native `fetch` if you want to.

```javascript
// userActions.js
import * as types from './actionTypes';

const apiURL = 'https://reqres.in/api/users'


const receiveUsers = (json) => {
  return {type: types.RECEIVE_USERS, users: json.data};
}

const fetchUsers = () => {
  return dispatch => {
    return axios.get(apiUrl)
    .then(response => dispatch(receiveUsers(response.data)));
  };
}

export { receiveUsers, fetchUsers}
```

Next, we will update the `./reducers/usersReducer.js` to import the actions types, and have a nice little case statement of how to handle each type of action. We will also update the `./reducers/rootReducer.js` with the necessary plumbing, as well our new `usersReducer`

```javascript
//rootReducer.js
import { combineReducers } from 'redux';
import users from './usersReducer';

const rootReducer = combineReducers({
  users
});

export default rootReducer;
```

```javascript
import initialState from '../store/initialState';
import {FETCH_USERS, RECEIVE_USERS} from '../actions/actionTypes';

 const users = (state = initialState.users, action) => {
  let newState;
  switch (action.type) {
    case FETCH_USERS:
      console.log('FETCH_USERS Action')
      return action;
    case RECEIVE_USERS:
      newState = action.users;
      console.log('RECEIVE_USERS Action')
      return newState;
    default:
      return state;
  }
}

export default users
```

## Setting Up Store

Now that we have some action types, and a reducer in our `rootReducer`, we need to get everything connected to the application. 

In the `./store/configureStore.js` file we will import `createStore` from `redux`, and our `rootReducer`.

We will pass to `createStore` our middleware ( `thunk` ), the `rootReducer` and some arguments to enable the dev tools, which are really nice when working with a redux app.

```javascript
import {createStore, applyMiddleware} from 'redux';
import rootReducer from '../reducers/rootReducer';
import thunk from 'redux-thunk';

export default function configureStore() {
  return createStore(
    rootReducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__(),
    applyMiddleware(thunk)
  );
}
```

To use this setup code we need to add our `store` to the `Provider` component. In the `index.js` file of our app, we need to import `Provider` from `react-redux` and have that wrap our `App` component. This component will now take the store we pass it as a prop, which we have returned from the function we just wrote in `configureStore`.

```javascript
import React from 'react';
import { render } from 'react-dom';
import { Provider } from 'react-redux'
import configureStore from './store/configureStore';
import App from './components/App';

const store = configureStore();

render(
  <Provider store={store}>
      <App />
  </Provider>,
  document.getElementById('app')
);
```

At this point, we have a lot the initial set up work set. Of course, we will need some components to actually dispatch an action or two, for us to see it all working together.

## A Connected Component

Let's add a new component that lists all our users . For this add a UsersList.js file. Then render this new component inside theApp component.

In addition to everything you are used for React we will also be importing `connect` from `react-redux` and bindActionCreators from redux , along with our actions from `./actions/userActions.js`. Note that bindActionCreators is for when you want to pass some action creators down to a component, and connect used to allow the component access to the redux application state we have implemented.

```javascript
import React, { Component } from 'react';
import {connect} from 'react-redux';
import {bindActionCreators} from 'redux';
import * as userActions from '../modules/actions/userActions'
```

We will need, in addition to all the normal set up of the component, two functions that we will ultimately be passing the `connect` as arguments. These are `mapStateToProps` and `mapDispatchToProps`. 

The first, `mapStateToProps` will hydrate the props of your component from the state of the application. The next, `mapDispatchToProps` ensures our actions have access to dispatch from `redux`.

```javascript
const mapStateToProps = (state) => {
  return {
    users: state.users
  }
}

const mapDispatchToProps = (dispatch) => {
  return {
    userActions: bindActionCreators(userActions, dispatch)
  }
}
```

Once we have all that set up, rather than exporting this react component itself, we will export the **connected component**, which is the result of the `connect` function we imported from `react-redux`.

```javascript
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(UserList)
```

## Fetching Users

Finally, we need to call the `fetchUsers()` function when the component has rendered. A good place to do that is when the `componentDidMount` life cycle event has been fired.

```javascript
componentDidMount() {
  this.props.userActions.fetchUsers()
}
```

The entire component should look something like this:

```javascript
import React, { Component } from 'react';
import { bindActionCreators } from 'redux'
import { connect } from 'react-redux'
import * as userActions from '../modules/actions/userActions'

class UserList extends Component {

  componentDidMount() {
    this.props.usersAction.fetchUsers()
  }

  render() {
    let listUsers
    listUsers = this.props.users.map(user => {
      return (
        <li
          style={{ listStyle: 'none' }}
          key={user.id}>
          <img
            src={user.avatar}
            alt={user.last_name} 
            style={{borderRadius: '50%', width: '50px', height: 'auto'}}/>
          {`${user.first_name} ${user.last_name}`}
        </li>
      )
    })
    return (
      <>
        User List
        <ul>
          {listUsers}
        </ul>
      </>
    );
  }
}

const mapStateToProps = (state) => {
  return {
    users: state.users
  }
}

const mapDispatchToProps = (dispatch) => {
  return {
    userActions: bindActionCreators(userActions, dispatch)
  }
}

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(UserList)
```