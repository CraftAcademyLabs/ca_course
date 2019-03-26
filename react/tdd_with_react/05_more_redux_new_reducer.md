## Let's get at it again

Lets go through adding a new reducer manually...

We will start by atting another attribute to `initialState`

```javascript
// /store/initialState.js
export default {
  users: [],
  greeting: 'Hello World' 
}
```

And modify our `App` component to connect to `redux`

```javascript
import { connect } from 'react-redux'

// implementation code ...
render() {
  return {
    <>
      <h1>{this.props.greeting ? this.props.greeting : 'Nothing to see!'}</h1>
    </>
  }
}

// Add this
const mapStateToProps = (state) => {
  return {
    greeting: state.greeting
  }
}

// and export App as a connected component
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(App)
```

This will NOT give us the `greeting` prop. We need to create a `greetingReducer`. It will be a simple one (no `actions`are needed, since we only want to display the value from `initialState`)

```javascript
import initalState from '../store/initialState'

const greetingReducer = (state = initalState.greeting) => {
  return state;
}

export default greetingReducer;
```

Now this should display "Hello World" on yoyr page.

And, consequently, we need to include the new reducer in `rootReducer`

```javascript
import { combineReducers } from 'redux'
import usersReducer from './usersReducer'
import greetingReducer from './greetingReducer'

const rootReducer = combineReducers({
  users: usersReducer,
  greeting: greetingReducer
})

export default rootReducer
```


## Add an action

We would like to use actions to change the greeting that is being displayed. Let's start by adding an action type:

```javascript
// actions/actionTypes.js
export const CHANGE_GREETING = 'CHANGE_GREETING'
```

and create a new firle `/actions/greetingActions.js`

```javascript
import * as types from './actionTypes'

const changeGreeting = () => {
  return {type: types.CHANGE_GREETING, greeting: 'Hello My Dear Friends'}
}

export { changeGreeting }
```

And modify the `greetingReducer` to look something like this:

```javascript
import initialState from '../store/initialState'
import { CHANGE_GREETING } from '../actions/actionTypes';

const greetingReducer = (state = initialState.greeting, action) => {
  if (action.type === CHANGE_GREETING) {
    let newState = action.greeting
    return newState
  } else {
    return state
  }
}

export default greetingReducer
```

We also need to make sure that the component we use map the actions (in my case the `SayHello`component):

```javascript
import { bindActionCreators } from 'redux'

import * as greetingActions from '../modules/actions/greetingActions'

// implementation code
const mapDispatchToProps = (dispatch) => {
  return {
    greetingActions: bindActionCreators(greetingActions, dispatch)
  }
}


// Make sure to include `mapDispatchToProps` in your `connect`
export default connect(
  mapStateToProps, mapDispatchToProps
)(SayHello)
```

Update the `render` function to display a `<button>`

```javascript
<button
  onClick={() => props.greetingActions.changeGreeting('')}
>
  Change greeting
</button>
```

Now, when you click the button, the greeting should change.

