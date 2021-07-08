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

