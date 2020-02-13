a sOur portfolio application is taking shape. In the previous section, we added a header and a footer to the page structure. Now, we want to extend our application with an "About Me" and a "Projects" section. There are many ways to achieve that, but for learning purposes, we will take a path that requires us to add dynamic routes to our application. The result we are looking for is something like this:

![](https://github.com/CraftAcademyLabs/ca_course/raw/master/week3/portfolio_challenge/assets/portfolio_v2_basic_navigation.gif)

## Router - the concept

Single-page applications (like the one we are building) rewrite sections of a page rather than loading entire new pages from a server. There are many examples of applications that do not render the entire page when you click a link or request new information from the server in another way. You've probably encountered this functionality on many of the popular sites out there.

A router allows our application to navigate between different components, changing the browser URL, modifying the browser history, and keeping the UI state in sync. Routing is of uttermost importance in almost every application's architecture. The larger your app becomes, the more your routing functionality becomes complex, from simple to deeply nested routing scenarios.

As mentioned before, at its core React focuses only on building user interfaces, it doesn’t have a built-in solution for routing.

[React Router](https://reacttraining.com/react-router/) is the most popular routing library for React. It allows us to define routes in the same declarative style. The `Route` component is perhaps the most important component in React Router to understand and learn to use well. Its most basic responsibility is to render some UI when a location matches the route’s path.

React Router provides two Routers for us to use: `BrowserRouter` and `HashRouter`. We need to decide which one to use, and the choice will mostly depend on our server architecture.

`BrowserRouter` is the recommended default, but it may require additional server configuration to handle dynamic requests. `HashRouter` is not suitable for all situations, but it can be a good solution for static websites.

In our Portfolio application, we will use `BrowserRouter` as our Router of choice.

In the following section, we will use some of the basic functionality React Router brings to the table. If you want to go deeper into the router visit [this resource](https://reacttraining.com/react-router/core/guides/philosophy) and read through the documentation and examples.

## Writing the test

The first thing we want to do is to add another integration test. Our user should be able to click through different tabs.

`$ touch cypress/integration/userCanNavigateThroughTheApplication.feature.js`

```js
// cypress/integration/userCanNavigateThroughTheApplication.feature.js

describe('User can navigate the app', () => {
  beforeEach(() => {
    cy.visit('http://localhost:3000');
  })


  it('to About tab',() => {
    cy.get('#about-tab').click();

    cy.get('#about-header').should('contain', 'About Me');

    cy.get('#projects-header').should('not.exist');
    cy.get('#hello').should('not.exist');
  })

  it('to My Projects tab',() => {
    cy.get('#projects-tab').click();

    cy.get('#projects-header').should('contain', 'My Projects');
    
    cy.get('#about-header').should('not.exist');
    cy.get('#hello').should('not.exist');
  })

  it('back to My Portfolio/Hello tab',() => {
    cy.get('#about-tab').click();
    cy.get('#header').click();

    cy.get('#hello').should('contain', 'Hello');

    cy.get('#projects-header').should('not.exist');
    cy.get('#about-header').should('not.exist');
  })
})
```

We test that the user can click through the tabs and see the content that is expected to be there. We are also making sure that we cant see the elements that are supposed to be visible on the other tabs, to make sure that the navigation works properly.

The test above is not how we want to structure our test. As we have mentioned previously, we only want **ONE** assertion for a single it block. The correct way of structuring this test looks like this.

```js
describe('User can navigate the app', () => {
  beforeEach(() => {
    cy.visit('http://localhost:3000');
  })

  describe('to About tab and it', () => {
    beforeEach(() => {
      cy.get('#about-tab').click();
    });

    it('displays About Me header', () => {
      cy.get('#about-header').should('contain', 'About Me');
    });

    it('displays component name in url', () => {
      cy.url().should("contain", "about");
    })

    it('does not display My Projects header ', () => {
      cy.get('#projects-header').should('not.exist');
    });

    it('does not display Hello world', () => {
      cy.get('#hello').should('not.exist');
    });
  });

  describe('to My Projects tab and it',() => {
    beforeEach(() => {
      cy.get('#projects-tab').click();
    });

    it('displays My Projects header', () => {
      cy.get('#projects-header').should('contain', 'My Projects');
    });

    it('displays component name in url', () => {
      cy.url().should("contain", "projects");
    })

    it('does not display About Me header ', () => {
      cy.get('#about-header').should('not.exist');
    });

    it('does not display Hello world', () => {
      cy.get('#hello').should('not.exist');
    });
  });

  describe('back to My Portfolio/Hello tab and it',() => {
    beforeEach(() => {
      cy.get('#about-tab').click();
      cy.get('#header').click();
    });

    it('displays Hello World', () => {
      cy.get('#hello').should('contain', 'Hello');
    });

    it('displays correct url', () => {
      cy.url()
        .should("not.contain", "projects")
        .and("not.contain", "about");    
    })

    it('does not display About Me header ', () => {
      cy.get('#about-header').should('not.exist');
    });

    it('does not display My Projects header', () => {
      cy.get('#projects-header').should('not.exist');
    });
  });
});
```

Let's make these tests go green!


## Getting started
As with every package, we will add the router using NPM and save it as a dependency.

```
$ npm i -S react-router-dom
// or
$ yarn add react-router-dom
```

We want to add 2 new components to our application. We will create them in `About.jsx` and `Projects.jsx` (in the `src` folder).

```js
// src/About.jsx

import React from "react";

const About = () => {
  return (
    <div className="ui main container">
      <h1 id="about-header" className="ui header">About Me</h1>
      <p>
        Ipsum dolor dolorem consectetur est velit fugiat. Dolorem provident
        corporis fuga saepe distinctio ipsam? Et quos harum excepturi dolorum
        molestias?
      </p>
    </div>
  );
};

export default About;
```

```js
// src/Projects.jsx

import React from "react";

const Projects = () => {
  return (
    <div className="ui main container">
      <h1 id="projects-header" className="ui header">My Projects</h1>
      <p>
        Ipsum dolor dolorem consectetur est velit fugiat. Dolorem provident
        corporis fuga saepe distinctio ipsam? Et quos harum excepturi dolorum
        molestias?
      </p>
    </div>
  );
};

export default Projects;
```

We also want to `import` those components, together with the `react-router-dom`, into our `index.js`.

```js
// src/index.js

import { BrowserRouter } from "react-router-dom";
import Projects from "./Projects";
import About from "./About";
```

Furthermore, we need to tell the application that we will be using the `BrowserRouter` when we hook the application into the `index.html`. We need to modify the `ReactDOM.render` function in `index.js` to the following markup:

```js
// src/index.js

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById("app")
);
```

## Switch and Route components

React Router has, among others, the `Switch` and `Route` components. The `Route` component renders a page if the current URL location matches the path prop specified in it. It also accepts `component`, `render` and `children` props. `Switch` will avoid inclusive route rendering and include a catch-all Route (if we add one at the bottom of the container).

We need to import both these components into our `index.js`.

```js
// src/index.js

import { Switch, Route, BrowserRouter } from "react-router-dom";
```

We will put them to use in our `return` of our `App` component.

```js
// src/index.js

const App = () => {
  return (
    <>
      <Header />
      <Switch>
        <Route exact path="/" component={Hello}></Route>
        <Route exact path="/about" component={About}></Route>
        <Route exact path="/projects" component={Projects}></Route>
      </Switch>
      <Footer />
    </>
  );
};
```

## Link and NavLink components

As the last step, we need to display some links in our `Header` component. We will use the `Link` and `NavLink` components (for learning purposes). Both of them are made available for use by the React Router package.

The `Link` component provides declarative, accessible navigation around an application. `NavLink` is a special version of the `Link` that will add styling attributes to the rendered element when it matches the current URL. We can style the active link using `activeClassName` or `activeStyle` prop.

We need to `import` both components to our `Header.jsx`.

```js
// src/Header.jsx

import { NavLink, Link } from "react-router-dom";
```

And add them to our render function.

```js
// src/Header.jsx

const Header = () => {
  return (
    <nav className="ui fixed inverted menu">
      <div className="ui container">
        <Link id="header" className="header item" to="/">
          My Portfolio
        </Link>
        <div className="right menu">
          <NavLink
            id="about-tab"
            className="ui item"
            activeStyle={{ fontWeight: "bold" }}
            to="/about"
          >
            About Me
          </NavLink>
          <NavLink
            id="projects-tab"
            className="ui item"
            activeStyle={{ fontWeight: "bold" }}
            to="/projects"
          >
            Projects
          </NavLink>
        </div>
      </div>
    </nav>
  );
};
```

## Wrap up
Run the tests now and everything should go green!
We now have basic navigation in place. Moving forward, we will start filling our portfolio with content.
