Next feature that we want to implement is that the user can save their cooper result.

So let's add a feature file for this:
`$ touch src/__features__/UserCanSavePerformanceData.js`

```js
require('../__mocks__/mocksConfig')

describe('User attempts save data', () => {

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
  })

  it('successfully', async () => {
    await page.type('input[id="distance"]', '1000')
    await page.select('select[id="gender"]', 'female')
    await page.type('input[id="age"]', '23')
    await page.click('#save-result')
    await expect(page).toMatch('Your entry was saved')
  })

  it('can save two different entries', async () => {
    await page.type('input[id="distance"]', '1000')
    await page.select('select[id="gender"]', 'female')
    await page.type('input[id="age"]', '23')
    await page.click('#save-result')
    await expect(page).toMatch('Your entry was saved')
    await page.type('input[id="distance"]', '1500')
    await page.click('#save-result')
    await expect(page).toMatch('Your entry was saved')
  })
})
```

When a user is logged in and have a cooper result, you should be able to save that result. 

Let's start with adding a conditional in our render method to show the `#save-result` button if the user is logged in and have a result.
Add this to the render method in the `DisplayCooperResult` component:

```js
render() {
  let results;
  let saveButton;

  if (this.props.authenticated === true) {
    saveButton = (
      <>
        <button id="save-result" onClick={this.saveCooperData.bind(this)}>Save entry</button>
      </>
    )
  }
  if (this.props.age !== '' && this.props.distance !== '') {
      results = (
        <>
          <p>{this.props.age} y/o {this.props.gender} running {this.props.distance} meters.</p> 
          <p>Result: {this.calculate()}</p>
          {saveButton}
        </>
      )
    }
  return (
    <div>
      {results}
    </div>
  )
}
```

If there is a result and the user is authenticated, then the `#save-result` button will show. We have an `onClick` on this button which calls on a function we have not defined yet, `saveCooperData`. Let's add that above the render method:

```js
// ...
async saveCooperData() {
    const result = this.calculate();
    try {
      let response = await saveData(result);
      this.props.entryHandler();
    } catch(error) {
      console.log(error);
    }
  }
```

The application will complain about `saveData()` not being defined. We need to add a new module to get this to work. Run:

`$ touch src/Modules/PerformanceData.js`

Add this:
```js
import axios from 'axios'

const apiUrl = 'http://localhost:3000/api/v1';

const saveData = (result) => {
  const currentUser = JSON.parse(sessionStorage.getItem(['current_user']));
  const path = apiUrl + '/performance_data';
  return new Promise((resolve, reject) => {
    axios.post(path, {
      performance_data: { data: { message: result }},
      user_id: currentUser.id
    })
    .then(response => {
      resolve(response.data.message);
    });  
  });
};
export { saveData }
```

You have to import his file to the `DisplayCooperResult` component.

So from our `DisplayCooperResult` component, we send in the result to this function. The first thing we do is grabbing the current user id in a variable. Then we make a post request to the backend with that current user's id and the cooper result. The response we get back gets sent back to saveCooperData function in the `DisplayCooperResult` component. If there is no error in the response, we call on the entryHandler. We have not defined the `entryHandler` yet, but let's do it now.

Add this to `App` component:

```js
entryHandler() {
  this.setState({ entrySaved: true });
}
```

We also have to set that new state in our constructor:

```js
constructor(props) {
    super(props);
    this.state = {
      // ...
      entrySaved: false
    }
```

Now we need to pass the entryHandler and `entrySaved` state to the `DisplayCooperResult` component:

```js
  <DisplayCooperResult
    distance={this.state.distance}
    gender={this.state.gender}
    age={this.state.age}
    authenticated={this.state.authenticated}
    entrySaved={this.state.entrySaved}
    entryHandler={this.entryHandler.bind(this)}
  />
```

If you run the feature test now, you will get an error that says "Text not found "Your entry was saved". That's not weird, we haven't rendered the message we get from the backend anywhere. If you take a look at logs for the backend, you will see that the performance data is being saved correctly. We need to add and modify some of our code to get it to go green.

First we need to modify the if statement in `DisplayCooperResult` component:

```js
render() {
  if (this.props.authenticated === true && this.props.entrySaved === false) {
    saveButton = (
      <>
        <button id="save-result" onClick={this.saveCooperData.bind(this)}>Save entry</button>
      </>
    )
  } else if (this.props.authenticated === true && this.props.entrySaved === true) {
    saveButton = (
      <>
        <p>Your entry was saved</p>
      </>
    )
  }

  // ..

}
```

Previously we modified the rendering of this component in the `App` component to pass in the state of `entrySaved`. So if we have saved the result we want to see `"Your entry was saved"`. Now we need to modify the `onChange` function in the app component so that every time we modify the inputs we can save the new result. 

```js
  onChange(event) {
    this.setState({
      [event.target.id]: event.target.value,
      entrySaved: false
    })
  }
```

If you run the test now, both test in the feature file should go green.

We want to be able to run these test without sending requests to the backend. Let's mock the response out. 

First we need to add some code to the `mocksConfig` file, so it intercepts the `/performance_data` request:

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
        return MockResponses.savingEntryResponse
    }
    
  }

  const requests = {
    'sign_in': {},
    'performance_data': {}
  }

  // ...
```

Now we need to add the response that we are calling on when puppeteer intercepts `/performance_data`

Add this to `mockResponses`:

```js
// ...
  savingEntryResponse: {
    status: 200,
    headers: {},
    body: JSON.stringify({
      message: "all good"
    })
  }
```

Now you will get all green when you run the tests without the backend running.
