Next feature is quite obvious, if we can save data, we want to be able to see all of the data we have saved. 

Start off with adding the feature test

`$ touch src/__features__/userCanSeeIndexOfSavedPerformanceData.feature.js`

```js
require('../__mocks__/mocksConfig')

describe('User attempts to view his/her performance data', () => {

  beforeAll(async () => {
    jest.setTimeout(10000)
    await page.goto('http://localhost:3001');
  });

  beforeEach(async () => {
    await page.reload()
    await page.click('#login')
    await page.type('input[id="email"]', 'johndoe@mail.com')
    await page.type('input[id="password"]', 'password')
    await page.click('button[id="submit"]')
    await page.waitFor(1000)
  })

  it('successfully', async () => {
    await page.waitFor(2000)
    await page.click('button[id="show-index"]')
    await page.waitFor(2000)
    await expect(page).toMatch('Below Average')
    await expect(page).toMatch('Average')
    await expect(page).toMatch('Above Average')
  })
})
```
If the user is logged in, he can press a button to list all of his previously saved results.

This time we are going to start with adding the mocks for this straight away. Let's start off with modifying some of the code in `mocksConfig`.

The case block for `performance_data` should look like this: 

```js
const createResponse = (path, params, request) => {
    let response
    switch (path) {
      case 'sign_in':
        let user
        user = MockResponses.mockedUserResponses.find(user => {
          return user.headers.uid === JSON.parse(params).email
        })
        response = user || MockResponses.missingUserResponse
        return response
      case 'performance_data':
        if ((request.method()) === 'POST') {
          response = MockResponses.savingEntryResponse
        } else if ((request.method()) === 'GET') {
          response = MockResponses.performanceDataIndexResponse
        }
        return response
    }
  }
```

We are hitting the same route when we are saving data, so we need to have an if statement which checks for the request method and based on that gives different mocked out responses. We now have to define the response we want to give when we are intercepting the request to get all saved data.

Add this to `mockResponses.js`
```js
  performanceDataIndexResponse: {
    status: 200,
    headers: {},
    body: JSON.stringify(
      {
        entries: [
          {
            data: {
              message: "Below Average"
            },
            id: 1,
            user_id: 1
          },
          {
            data: {
              message: "Average"
            },
            id: 2,
            user_id: 1
          },
          {
            data: {
              message: "Above Average"
            },
            id: 3,
            user_id: 1
          }
        ]
      }
    )
  }
```

These are the entries that we want to see when we press the button to display past entries. 

Let's start with adding the button to show the saved results.
In the `App` component, we need to modify the if statement where the user is authenticated, modify it to look like this:

```js
  let performanceDataIndex;

  if (this.state.authenticated === true) {
    user = JSON.parse(sessionStorage.getItem('credentials')).uid;
    renderLogin = (
      <p>Hi {user}</p>
    )
    performanceDataIndex = (
      <button id="show-index" onClick={() => this.setState({ renderIndex: true })}>Show past entries</button>
    )
  }
```

So when we click on this button, we change a state called `renderIndex` to true. Make sure that you have this state set in the constructor, it's important that it is set to false there.

We need to render whats inside the variable we created as well.

```js
return (
  // ..
  <DisplayCooperResult
    distance={this.state.distance}
    gender={this.state.gender}
    age={this.state.age}
    authenticated={this.state.authenticated}
    entrySaved={this.state.entrySaved}
    entryHandler={this.entryHandler.bind(this)}
  />
  {performanceDataIndex}
)
```

If you run the test now it will complain about not finding the text that we are looking for. Its time to create a new component now.

`$ touch src/Components/DisplayPerformanceData.js`

```js
import React, { Component } from 'react';
import { getData } from '../Modules/PerformanceData';

class DisplayPerformanceData extends Component {
  constructor(props) {
    super(props)
    this.state = {
      performanceData: null
    }
  }
  componentDidMount() {
    this.getPerformanceData()
  }

  async getPerformanceData() {
    let result = await getData();
    this.setState({performanceData: result.data.entries}, () => {
      this.props.indexUpdated();
    })
  }

  render () {
    let dataIndex;

    if (this.props.updateIndex === true) {
      this.getPerformanceData();
    }
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

We have the function `getPerformanceData`. This is what's going to run every time someone presses the button to show previous entries or when someone has saved a new result. The way we make it run every time the component is rendered is with `componentDidMount`. The response that we get from the backend will be stored in `perfromanceData` state. This will also call on a function we will add later to the `App` component called `indexUpdated`.

Then we have the render method. First, we set a variable called dataIndex that we are going to use for displaying all saved performance data entries. After that, we have an if statement where we check the props if the `updateIndex` state is true, if that's the case the app will run `getPerformanceData` again to get the latest saved entries.

The next if block checks if the `performanceData` is empty or not. If it's not empty, then its going to loop through all entries of performance data that is stored in the `perfromanceData` state and place it in the `dataIndex` variable.

If you run the test now, you will get the same error message as before. That's not that weird because we have not even rendered the new component we have just created in the `App` component. First off, make sure to import the `DisplayPerformanceData` component to the `App` component. 

Add this inside the if statement for the user to be authenticated:

```js
if (this.state.authenticated === true) {
  // ...
  if (this.state.renderIndex === true) {
    performanceDataIndex = (
      <>
        <DisplayPerformanceData
          updateIndex={this.state.updateIndex}
          indexUpdated={this.indexUpdated.bind(this)}
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

Add the `indexUpdated` function and update `entryHandler` so it looks like this:

```js
entryHandler() {
    this.setState({ entrySaved: true, updateIndex: true });
}

indexUpdated() {
  this.setState({ updateIndex: false });
}
```

Every time we save a new result we want to update the index and when we have sent the request for the index of saved performance data we need to set the state to false, otherwise, the application would continue to make GET requests to the backend.

If we try running the test or running the application, it will complain about how `getData` in the `getPerformanceData` function is not defined.

If we take a look in our `PerformanceData` module we can see that we don't have something like that defined.

Add this to the `PerfromanceData` module:

```js
const saveData = () => {
//...
}

const getData = () => {
  const headers = JSON.parse(sessionStorage.getItem(['credentials']));
  const path = apiUrl + '/performance_data';
  return new Promise((resolve, reject) => {
    axios.get(path, {
      headers: headers
    })
    .then(response => {
      storeAuthHeaders(response);
      resolve(response);
    });
  });
};

export { getData, saveData }
```

Here we make a GET request to `/performance_data` where we pass in the credentials that we have stored in `sessionStorage`.

The response we get from here is what the application will put in the `perfromanceData` state. We also update the credentials with what we got from the response headers.

If you run the test now, everything should go green!
