## Our First Test

Let's start using Cypress. To begin with, create a new test file in the `cypress/integration` folder. I will use a slightly different naming standard than the one proposed in the examples (more on this topic later).

```bash
$ touch cypress/integration/displayEmployeeList.feature.js
```

Add a `describe` block:

```javascript
describe('Display list of employees', () => {
  // Our tests will go here
})
```

Time to start cranking out the tests! Let's start with a simple one - can we visit the site and see the text we placed in the `section` we called `header`?

```javascript
it('when user visits the page', () => {
    cy.visit('http://localhost:3000')
    cy.get('section[name="header"]')
      .should('contain', 'Employee list')
})
```

Remember - all **`it`** blocks need to go inside the `describe` block.

Before we run our tests, we need to keep our localhost active in another terminal. Start it by running `$ yarn start` or `$ npm start`. (Moving forward, we will only type out the `yarn` commands - if you want to use `npm`, you just go ahead and use it...)

Let's launch Cypress by executing the script: `$ yarn run cy:open`. A cypress window will pop open with a list of available tests (in out case, just 1 file). Clicking the file name will trigger the test runner and a new broweser window will pop up.

![](./employee_list_cypress_runner.png)

There's not much to see here, but the test we just wrote should pass.


## Display employees

Okay, so our first test is passing. That's great. Let's move on.

We want to display details for 5 employees. Why 5? Well, we have to decide on something, and we want to be in control of the test data and of what we are actually showing to the user.

Let's modify our feature with the following code (inside the `it` block we added before):

```javascript
cy.get('section[name="main"]').within(() => {
  cy.get('li')
    .should('have.length', 5)
})
```

If you examine the error message the runner is giving you, you'll see the following message

```
CypressError: Timed out retrying: Expected to find element: 'section[name="main"] li', but never found it.
```

Alright, let's do some thinking...

We want to display a collection of users. So, let's consider extracting that functionality to a separate component. We need to give the component a name. Why not call it `EmployeeList`?

We will want this component to fetch the employee data from our back-end. Let's add that to our TODO list. Another thing to add to that TODO is that we want the component to retun a unnumbered list (`<ul></ul>`) with a list item  (`<li></li>`) for each employee...

Those list items should contain an image, first name - last name, and email... (check the lo-fi!)

Alright, the TODO list is growing - it **might** be a good idea to write it down.

Let's pretend for a moment that we have the component done and ready (we don't - but we do test the code we wish we had, right?). We can update our application code to start using it. We will add an `import` and ask our `render` method to use it:

```javascript
import React, { Component } from 'react';
import EmployeeList from './components/EmployeeList'

class App extends Component {
  render() {
    return (
      <>
        <section name="header">
          <h1>Employee list</h1>
        </section>
        <section name="main">
          <EmployeeList />
        </section>
      </>
    );
  }
}

export default App;
```

If you check the Cypress runner, you will probably see something like this:

![](./employee_list_missing_component.png)

## The "Inside"
We will move from the "Outside" to the "Inside" and make sure we test drive the development of our new component.


## The EmployeeList spec

We could start with following specification as a starting point:

```javascript
import { render, screen } from '@testing-library/react';
import EmployeeList from '../components/EmployeeList';

beforeEach(() => {
  render(<EmployeeList />);
});
it('renders learn react link', () => {
  const lidtElement = screen.getByRole('list')
  expect(lidstElement.children.length).toBe(5);
});

```
Make sure to run the test. Thay will fail for you, of course. Let's add some implementation code.

Create a subfolder in `src`, called `components`. In that folder, we will create a component file: `EmployeeList.jsx`

```js
import React, { Component } from 'react'

export default class EmployeeList extends Component {
  state = {
    employees: [
      { "id": 1, "first_name": "George", "last_name": "Bluth", "avatar": "https://s3.amazonaws.com/uifaces/faces/twitter/calebogden/128.jpg" },
      { "id": 2, "first_name": "Janet", "last_name": "Weaver", "avatar": "https://s3.amazonaws.com/uifaces/faces/twitter/josephstein/128.jpg" },
      { "id": 3, "first_name": "Emma", "last_name": "Wong", "avatar": "https://s3.amazonaws.com/uifaces/faces/twitter/olegpogodaev/128.jpg" },
      { "id": 4, "first_name": "Eve", "last_name": "Holt", "avatar": "https://s3.amazonaws.com/uifaces/faces/twitter/marcoramires/128.jpg" },
      { "id": 5, "first_name": "Charles", "last_name": "Morris", "avatar": "https://s3.amazonaws.com/uifaces/faces/twitter/stephenmoon/128.jpg" }
    ]
  }

  render() {
    let employeeList
    employeeList = this.state.employees.map(employee => {
      return (
        <li key={employee.id}>
          {`${employee.first_name} ${employee.last_name}`}
        </li>
      )
    })
    return (
      <ul role="list">
        {employeeList}
      </ul>
    )
  }
}
```

Now, this will make our test pass. But, this is of course nonsense. We have hard coded values in our component's stae. We want to fetch these from the api.

Let's modify the test a bit.



These specs will do to things for us:

1. make sure that we are making use of `axios` to make a `get` request when the component is being used, and

2. that once state has been updated with a list of `employees`, the component is re-rendered with the right content.

We need to start running the tests and work out way through the error messages. If we read them carefully, we WILL be pointed in the right direction.


This is an excercise in reading the test output, implementing the right code and progress one step at the time. **You can do it!**

At the end of the tunnel, your `EmployeeList` could look something like this:

```javascript
import React, { Component } from 'react';
import axios from 'axios'

class EmployeeList extends Component {
  state = {
    employees: []
  }

  componentDidMount() {
    this.fetchEmployees()
  }

  async fetchEmployees() {
    let employees = await axios.get('https://reqres.in/api/users?per_page=5')
    this.setState({ employees: employees.data.data })
    // ?!? "data.data" - really?
    // Well, check out the response by setting a breakpoint just
    // before this line withe `debugger`
  }

  render() {
    let employeeList
    employeeList = this.state.employees.map(employee => {
      return (
        <li key={employee.id}>
          {`${employee.first_name} ${employee.last_name}`}
        </li>
      )
    })

    return (
      <ul>
        {employeeList}
      </ul>
    );
  }
}
export default EmployeeList;
```

The interesting thing to pay attention to is what happens with your acceptance test. Once the unit test is passing, you should shift your attention to the Cypress runner and examine the status. Last time we checked, the error was quite extensive, as we tried to use the `<EmployeeList>` component even before it was created. Now that it is in place, we can hopefully look at a green state of the acceptance test. The Acceptance - Unit Test approach has come full cycle.
