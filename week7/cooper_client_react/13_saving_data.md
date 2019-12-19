Next feature that we want to implement is that the user can save their cooper result.

So let's add a feature file for this:

`$ touch cypress/integration/userCanSavePerformanceData.spec.js`
```
describe('User attempts save data', () => {

  beforeEach(function() {
    cy.visit('http://localhost:3001');
    cy.route({
      method: 'POST',
      url: 'http://localhost:3000/api/v1/auth/sign_in',
      response: 'fixture:login.json',
      headers: {
        "uid": "user@mail.com"
      }
    })
    cy.get('#login').click();
    cy.get('#login-form').within(() => {
      cy.get('#email').type('user@mail.com')
      cy.get('#password').type('password')
      cy.get('button').click()
    })
  });

  it('successfully', () => {
    cy.get('input[id="distance"]').type('1000')
    cy.get('select[id="gender"]').select('female')
    cy.get('input[id="age"]').type('23')
    cy.get('#save-result').click()
    cy.contains('Your entry was saved')
  })

  it('can save two different entries', () => {
    cy.get('input[id="distance"]').type('1000')
    cy.get('select[id="gender"]').select('female')
    cy.get('input[id="age"]').type('23')
    cy.get('#save-result').click()
    cy.contains('Your entry was saved')
    cy.get('input[id="distance"]')
      .clear()
      .type('1500')
    cy.get('#save-result').click()
    cy.contains('Your entry was saved')
  })
})
```

We want to be able to run these test without sending requests to the backend. Let's mock the response out.

We need to update our `beforeEach` block in our feature:
```
describe('User attempts save data', () => {

  beforeEach(function() {
    cy.visit('http://localhost:3001');
    cy.server();
    cy.route({
      method: 'POST',
      url: 'http://localhost:3000/api/v1/performance_data',
      response: 'fixture:saving_entry_response.json'
    })
    cy.route({
      method: 'POST',
      url: 'http://localhost:3000/api/v1/auth/sign_in',
      response: 'fixture:login.json',
      headers: {
        "uid": "user@mail.com"
      }
    })
    cy.get('#login').click();
    cy.get('#login-form').within(() => {
      cy.get('#email').type('user@mail.com')
      cy.get('#password').type('password')
      cy.get('button').click()
    })
  });
// ...
})
```

Now we need to add the response that we are calling on when cypress intercepts `/performance_data`

We need to create a new fixture file:

`touch cypress/fixtures/saving_entry_response.json`

Add this to it:
```
{
  "status": 200,
  "headers": {},
  "body": {
    "message": "all good"
  }
}
```

When a user is logged in and have a cooper result, he should be able to click a button to save that result.

Let's start with adding a conditional in our render method to show the `#save-result` button if the user is logged in and have a result. Add this to the render method in the `DisplayCooperResult` component:
```
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
```
// ...
async saveCooperData() {
  const result = this.calculate();
  try {
    await saveData(result);
    this.props.entryHandler();
  } catch(error) {
    console.log(error);
  }
}
```

The application will complain about `saveData()` not being defined. We need to add a new module to get this to work. Run:

`$ touch src/Modules/PerformanceData.js`

Add this:
```
import axios from 'axios'
import { storeAuthCredentials } from './Auth'

const apiUrl = 'http://localhost:3000/api/v1';

const saveData = async (result) => {
  let headers = await sessionStorage.getItem("credentials");
  headers = JSON.parse(headers);
  headers = {
    ...headers,
    "Content-type": "application/json",
    Accept: "application/json"
  };
  const path = apiUrl + '/performance_data';
  return new Promise((resolve, reject) => {
    axios.post(path, {
      performance_data: { data: { message: result }}
    }, {
      headers: headers
    })
    .then(response => {
      storeAuthCredentials(response);
      resolve(response.data.message);
    });  
  });
};

export { saveData }
```

You have to import this file to the `DisplayCooperResult` component.

`import { saveData } from '../Modules/PerformanceData';`

So from our `DisplayCooperResult` component, we send in the result to this function. The first thing we do is grabbing the credentials we have stored in `sessionStorage`. Then we make a post request to the backend with those credentials and the cooper result. The response we get back gets sent back to saveCooperData function in the `DisplayCooperResult` component. We also call on the function `storeAuthHeaders` we have in the `Auth` module to update the credentials that we get in the response. If there is no error in the response, we call on the entryHandler. We have not defined the `entryHandler` yet, but let's do it now.

Add this to `App` component:
```
entryHandler() {
  this.setState({ entrySaved: true });
}
```

We also have to set that new state in our constructor:
```
constructor(props) {
  super(props);
  this.state = {
    // ...
    entrySaved: false
  }
}
```

Now we need to pass the entryHandler and `entrySaved` state to the `DisplayCooperResult` component:
```
  <DisplayCooperResult
    distance={this.state.distance}
    gender={this.state.gender}
    age={this.state.age}
    authenticated={this.state.authenticated}
    entrySaved={this.state.entrySaved}
    entryHandler={this.entryHandler.bind(this)}
  />
```

If you run the feature test now, you will get an error that says `"Text not found "Your entry was saved"`. That's not weird, we haven't rendered the message we get from the backend anywhere. If you take a look at logs for the backend, you will see that the performance data is being saved correctly. You can see the logs in the terminal where you started up the rails server.

We need to add and modify some of our code to get it to go green.

First we need to modify the if statement in `DisplayCooperResult` component:
```
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
  if (this.props.age !== '' && this.props.distance !== '') {
    results = (
    // ...
    )
  }
  // ...
}
```

Previously we modified the rendering of this component in the `App` component to pass in the state of `entrySaved`. So if we have saved the result we want to see `"Your entry was saved"`. Now we need to modify the `onChange` function in the app component so that every time we modify the inputs we can save a new result.
```
  onChange(event) {
    this.setState({
      [event.target.id]: event.target.value,
      entrySaved: false
    })
  }
```
If you run the test now, both test in the feature file should go green. If not, go over the code again.