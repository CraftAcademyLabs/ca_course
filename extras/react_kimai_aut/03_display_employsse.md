## Display employees

Okay, so our first test is passing. That's great. Let's move on.

We want to display details for 5 employees. Why 5? Well, we have to decide for something, and we want to be in control of the test data and of what we are actually showing the user. 

Let's modify our feature with the following code (inside the `it` block we added before):

```javascript
cy.get('section[name="main"]').within(() => {
  cy.get('li')
    .should('have.length', 5)
})
```

If you examine the eror message the unner is giving you, you'll see the following message

```
CypressError: Timed out retrying: Expected to find element: 'section[name="main"] li', but never found it.
```

Alright, let's do some thinking...

We want to display a collection of users, ight. So, let's consider extracting that functionality to a separate component. We need to give the component a name. Why not call it `EmployeeList`? 

We will want this component to fetch the employee data from our back-end. Let's add that to our TODO list. Anotherr thisn to add to that TODO is that we want the component to retun a unnumbered list (`<ul></ul>`) with a list item  (`<li></li>`)for each employee...

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

RRemember the TODO list we wrote a few minutes ago? Let's put it to work and write a specification for the `<EmployeeList>` component we want to build.












<!-- enzyme enzyme-adapter-react-16 -->