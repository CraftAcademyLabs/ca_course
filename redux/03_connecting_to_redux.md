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

