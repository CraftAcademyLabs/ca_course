Next feature is quite obvious, if we can save data, we want to be able to see all of the data we have saved.

Start off with adding the feature test

`$ touch cypress/integration/userCanSeeIndexOfSavedPerformanceData.feature.js`
```js
describe('User attempts to view his/her performance data', () => {
  before(function() {
    cy.server();
    cy.route({
      method: 'GET',
      url: 'http://localhost:3000/api/v1/performance_data',
      response: 'fixture:performance_data_index.json'
    })
    cy.route({
      method: 'POST',
      url: 'http://localhost:3000/api/v1/auth/sign_in',
      response: 'fixture:login.json',
      headers: {
        "uid": "user@mail.com"
      }
    })
    cy.visit("/");

    cy.get('#login').click();
    cy.get('#login-form').within(() => {
      cy.get('#email').type('user@mail.com')
      cy.get('#password').type('password')
      cy.get('button').contains('Submit').click()
    })
  });

  it('successfully', async () => {
    cy.get('#show-index').click()
    cy.get('#index').within(() => {
      cy.contains('Below Average')
      cy.contains('Average')
      cy.contains('Above Average')
    })
  })
})
```

If the user is logged in, he can press a button to list all of his previously saved results.

This time we are going to start with adding the mocks for this straight away.

We need to create a new fixture file:

`touch cypress/fixtures/performance_data_index.json`

Add this to it:
```json
{
  "entries": [
    {
      "data": {
        "message": "Below Average"
      },
      "id": 1,
      "user_id": 1
    },
    {
      "data": {
        "message": "Average"
      },
      "id": 2,
      "user_id": 1
    },
    {
      "data": {
        "message": "Above Average"
      },
      "id": 3,
      "user_id": 1
    }
  ]
}
```

These are the entries that we want to see when we press the button to display past entries.

Let's start with adding the button to show the saved results. In the `App` component, we need to modify the if statement where the user is authenticated, modify it to look like this:
```js
// App.jsx

  let performanceDataIndex;
  // ...
  case authenticated:
    renderLogin = (
      <p>Hi {JSON.parse(sessionStorage.getItem("credentials")).uid}</p>
    );
    performanceDataIndex = (
      <button id="show-index" onClick={() => this.setState({ renderIndex: true })}>Show past entries</button>
    )
    break;
```
So when we click on this button, we change a state called `renderIndex` to true. Make sure that you have this state set in the constructor, it's important that it is set to false there.

We need to render whats inside the variable we created as well.
```js
// App.jsx

return (
  // ..
  <DisplayCooperResult
    distance={this.state.distance}
    gender={this.state.gender}
    age={this.state.age}
    authenticated={this.state.authenticated}
    entrySaved={this.state.entrySaved}
    entryHandler={() => this.setState({ entrySaved: true, updateIndex: true })}
  />
  {performanceDataIndex}
)
```

If you run the test now it will complain about not finding the text that we are looking for. Its time to create a new component now.

`$ touch src/components/DisplayPerformanceData.jsx`
```js
import React, { Component } from 'react';
import { saveData } from "../modules/performanceData";

class DisplayPerformanceData extends Component {
  state = {
    performanceData: null
  }

  componentDidMount() {
    this.getPerformanceData()
  }

  componentDidUpdate(prevProps) {
    if (this.props.updateIndex != prevProps.updateIndex) {
      this.getPerformanceData()
    }
  }

  async getPerformanceData() {
    let result = await getData();
    this.setState({performanceData: result.data.entries}, () => {
      this.props.indexUpdated();
    })
  }

  render () {
    let dataIndex;

    if (this.state.performanceData != null) {
      dataIndex = (
        <div>
          {this.state.performanceData.map(item => {
            return <div key={item.id}>{item.data.message}</div>
          })}
        </div>
      )
    }

    return (
      <div>
        {dataIndex}
      </div>
    )
  }      
}

export default DisplayPerformanceData
```

So what is happening here?

First, we have a constructor where we set a state called `performanceData` to `null`. We are going to use this state to store the collection of performance data entries that we will get from the backend.

We have the function `getPerformanceData`. This is what's going to run every time someone presses the button to show previous entries or when someone has saved a new result. The way we make it run every time the component is rendered is with `componentDidMount`. The response that we get from the backend will be stored in `performanceData` state. This will also call on a function we will add later to the `App` component called `indexUpdated`.

Then we have the render method. First, we set a variable called dataIndex that we are going to use for displaying all saved performance data entries. 

In the `componentDidUpdate()` we check props if the `updateIndex` state is true, if that's the case the app will run `getPerformanceData` again to get the latest saved entries.

The next if block checks if the `performanceData` is empty or not. If it's not empty, then its going to loop through all entries of performance data that is stored in the `performanceData` state and place it in the `dataIndex` variable.

If you run the test now, you will get the same error message as before. That's not that weird because we have not even rendered the new component we have just created in the `App` component. First off, make sure to import the `DisplayPerformanceData` component to the `App` component.

Add this inside the if statement for the user to be authenticated:
```js
// App.jsx

case authenticated:
  // ...
  if (this.state.renderIndex) {
    performanceDataIndex = (
      <>
        <DisplayPerformanceData
          updateIndex={this.state.updateIndex}
          indexUpdated={() => this.setState({ updateIndex: false })}
        />
        <button onClick={() => this.setState({ renderIndex: false })}>Hide past entries</button>
      </>
    )
  } else {
    performanceDataIndex = (
      <button id="show-index" onClick={() => this.setState({ renderIndex: true })}>Show past entries</button>
    )
  }
}
```

Here we render the `DisplayPerformanceData` component if the user has pressed the button to do so. We pass in a new state that we have seen mentioned in the `DisplayPerfromanceData` component called `updateIndex` and we bind a new function called `indexUpdated` to it. We also add a button to set the `renderIndex` state to false, which means that the application won't render the index.

Update `entryHandler` so it looks like this:
```js
// App.jsx
<DisplayCooperResult
  distance={this.state.distance}
  gender={this.state.gender}
  age={this.state.age}
  authenticated={this.state.authenticated}
  entrySaved={this.state.entrySaved}
  entryHandler={() => this.setState({ entrySaved: true, updateIndex: true })}
/>
```

Every time we save a new result we want to update the index and when we have sent the request for the index of saved performance data we need to set the state to false, otherwise, the application would continue to make GET requests to the backend.

If we try running the test or running the application, it will complain about how `getData` in the `getPerformanceData` function is not defined.

If we take a look in our `PerformanceData` module we can see that we don't have something like that defined.

Add this to the `PerformanceData` module:
```js
const saveData = () => {
//...
}

const getData = async () => {
  let headers = await sessionStorage.getItem("credentials");
  headers = JSON.parse(headers);
  headers = {
    ...headers,
    "Content-type": "application/json",
    Accept: "application/json"
  };

  const response = await axios.get("/performance_data", {
    headers: headers
  });

  return response;
};

export { getData, saveData }
```

Here we make a GET request to `/performance_data` where we pass in the credentials that we have stored in `sessionStorage`.

The response we get from here is what the application will put in the `performanceData` state. We also update the credentials with what we got from the response headers.

If you run the test now, everything should go green!