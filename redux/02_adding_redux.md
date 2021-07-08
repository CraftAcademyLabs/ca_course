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

