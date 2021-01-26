## Getting started

We will build a small application that will allow us to display a list of employees in an organization and allow us to update some basic details about an employee, and even delete an employee record.

The we can start with a pretty simple lo-fi that can guide us:

![](./employees_first_draft.png)

This view will display a list of employees with a cta (call to action) to view details about an individual employee. Clicking on the button will add an overlay to the view and display a modal (or pop-up) with the employee details. There should be at least 2 cta's for further actions the user will be able to undertake (edit and delete the employee). For now though, there will no functionality attached to them.

![](./employees_first_draft_modal.png)

Alright, let's create the first User story and a scenario for this view.

```
As a user of the application
In order to be able to work with employee management
I would like to access a list of employees.
```

Okay, now let's break this down into a check-list of things that need to happen:

1. The user access the application using her/his browser
2. The application fetches the list of employees from the company's internal systems (exposed to the application by a Rest API endpoint)
3. A view with the employee data is rendered and displayed in the users browser.

We can start with that, and write a second User Story once we've finished implementing this first one.

## The tools
Let's wdo some set-up. We've decided (or it has been decided for us) that we will use ReactJS in this project. We will use `create-react-app` tol to scaffold the application. **Create React App** is a tool (built by developers at Facebook) that gives you a massive head start when building React apps. It saves you from time-consuming setup and configuration. You simply run one command and Create React App sets up the tools you need to start your React project. Create React App uses Webpack and Babel under the hood, but it generates only the files you need to work on your React project. If you’ve built React apps before, you’re not going have access to configuration files for Webpack, Babel, ESLint, etc. That way, you focus on just building your app.

Once the application is generated, we want to clean up some files and remove a lot of the auto-generated code (Yes, the tool does create some overhead, and we need to remove some unnecessary files).

```bash
$ yarn create react-app employee_list
$ npx create-react-app employee_management
```

Once you've cleaned up your project files, your structure should look like this:

![](./employee_list_ide_after_code_cleaning.png)



Now, we want to make some decisions regarding the testing frameworks we want to use.

[Cypress](https://www.cypress.io/) is a Javascript End to End testing framework.

![](https://github.com/tochman/ca_course-1/blob/master/react/tdd_with_react/cypress_landing_page.png?raw=true)

What Cypress brings to the table, is a solid platform for writing and automating UI tests. Powerful features, well written documentation and a very straight forward configuration. Just the way we like it!

[Jest]() and [React Teasting Library](https://testing-library.com/docs/react-testing-library/intro). Jest is a JavaScript test runner, that is, a JavaScript library for creating, running, and structuring tests. Jest ships as an NPM package, and it comes pre-installed with the **Create React App** tool. The React Testing Library is a very light-weight solution for testing React components that work with actual DOM nodes and allow for simulated interactions with the compenents.

**We will use both end-to-end and component tests in our flow. **

# Cypress

**Step 1**: Install `cypress` and save it as a development dependency:

```bash
$ yarn add cypress --dev
// or use npm
$ npm i cypress --save-dev
```

**Step 2**: Once we are done with installation, we can launch cypress by

```bash
$ ./node_modules/.bin/cypress open
```
This command will run a configuration script and add a bunch of example tests that can be a reference for you, and help you to get to know the various Cypress commands.

![](./employee_list_cypress_setup.png)

**Step 3**: We need to add a script to our `package.json` to help us accesss the test runner in a smoother way. I suggest that you add this to the `scripts` section of the `package.json` file:

```json
"cy:open": "yarn start --silent & cypress open"
```

I also would like you to modify the `eslintConfig` section to look like this (we want to avoid warnings in our IDE that implies that the `cy` object is undefined):

```json
"eslintConfig": {
  "extends": "react-app",
  "globals": {
    "cy": "cy"
  }
}
```

Cypress comes with TypeScript type declarations included. Modern text editors can use these type declarations to show IntelliSense inside spec files. The simplest way to see IntelliSense when typing a Cypress command or assertion is to add a triple-slash directive to the head of your test file. This will turn the IntelliSense on a per file basis. paste the comment line below into your test (on top).

```javascript
/// <reference types="Cypress" />
```

# Jest and React Testing Library

When you scaffold a new react application using Create React App and examine the folder and file structure, you'll find a test file in the `src` folder named `App.spec.js` , It looks something like this:

```js
import { render, screen } from '@testing-library/react';
import App from './App';

test('renders learn react link', () => {
  render(<App />);
  const linkElement = screen.getByText(/learn react/i);
  expect(linkElement).toBeInTheDocument();
});
```

This **IS** a component test written using React Testing Library and executed with Jest.We will write this kind of tests for our components, allthough we will structure them a bit differently.

You can go ahead and run this test by issuing the `yarn test` command in your terminal.
