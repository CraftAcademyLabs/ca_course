As the first challenge of this week, we will be returning to the BMI calculator. You have build this application once before, in Vanilla JavaScript. This means that you are familiar with the domain and the requirements.

This time around, we will build the BMI calculator in React and we will follow the Acceptance - Unit Test Cycle.

## Getting started

First off, we need to create the react application. we will use `create-react-app` to do so. When we scaffold our react application with it we get the basic project structure as well as Jest (the testing framework) partially configured.

Run this command in the terminal to create a React application:

`npx create-react-app bmi_calculator`

Go inside of the project and run `yarn start` to fire app the local server and navigate to `localhost:3000` to see that everything works fine.


## Configure Jest with Enzyme for Component testing

Now we are going to configure Jest so we can use it to unit test, or as we call it in React land, Component tests! We are going to use `jest`, `enzyme` to do this. So as usual, we need to add some packages. Run:

`yarn add enzyme enzyme-adapter-react-16 --dev`

Now we need to configure `enzyme`. Update the `setupTest.js` file to look like this:

```js
import { configure } from "enzyme";
import Adapter from "enzyme-adapter-react-16";

configure({ adapter: new Adapter() });
```

React automatically reads this file and will use enzyme as an adapter for running jest.

Enzyme exports 2 important functions for us:

- [Shallow](https://airbnb.io/enzyme/docs/api/shallow.html) this mounts the component and only the component. Children will not be mounted.
- [Render](https://airbnb.io/enzyme/docs/api/ReactWrapper/mount.html) this mounts the component and all child components.

Thats the configuration for Jest and Enzyme!

## Configure Acceptance testing

We are going to use [Cypress](https://www.cypress.io/) to write our acceptance test for this application.

### Installing Cypress

**Step1**: You need to run:

`npm i cypress --save-dev`

OR

`yarn add cypress --dev`

That's it. No, really! we are done here.

### Set Up Cypress

Once we are done with the installation, we can launch cypress by:

`yarn run cypress open`

But that's far too long, so we create a shortcut in our `package.json` by inserting `"cy:open": "yarn start --silent & cypress open"` in `scripts`.

If you use npm, the script should look like this `"cy:open": "npm start --silent & cypress open"`

Then we can open cypress by `npm run cy:open` or `yarn run cy:open`

Once we run cypress for first time, it will create scaffolded folder, with a lot of example tests (**Have a look at them, to get a sense of how tests should be written**).

Our tests are stored in `Integration` folder and `fixtures` folder will contain JSON files, which we will introduce during the course of this document. You can also delete the example tests in the `integration` folder.

Configure our `baseUrl` in `cypress.json` file:

```json
// cypress.json
{
  "baseUrl": "http://localhost:3000"
}
```

`baseUrl` is a something we can call on when we tell Cypress which URL it should go to. Instead of writing `http://localhost:3000`, we can call on that URL by writing `/`.

Now we have Cypress successfully configured.

## Cleaning

Start off with cleaning the `src/App.js`

Convert the file to a class, change the file extension to `jsx`, remove everything that is in the div with the class name `"App"`. Also, remove the import of the logo at line two.

The file should look like this:

```js
// src/App.jsx

import React, { Component } from "react";

class App extends Component {
  render() {
    return <div></div>;
  }
}

export default App;
```