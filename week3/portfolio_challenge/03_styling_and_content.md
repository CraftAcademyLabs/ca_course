
Let's move on with our Portfolio AND learn about React in the meantime. Don't forget why we are doing this - having a portfolio site is fun, and that is why we chose to build one for this exercise. But the real reason we are doing all of this is to learn to build engaging user interfaces with React.

The endgame for this section of the course is this:

![](https://github.com/CraftAcademyLabs/ca_course/raw/master/week3/portfolio_challenge/assets/portfolio_v2_hello_world.png)

We want to add a header and a footer to our application, and we want to add a little bit of styling to it. With that foundation, we will be able to move on and add some content to our portfolio site.

## Create the page structure

If you look at the screenshot above, you can see that the application has changed since we left off in the previous section. We now have a header, the main content area (the "Hello World" part. Yes, we WILL remove it soon enough) and a footer section.

We will make the changes to our code by creating 2 new components (a `Header` and a `Footer` component), reference them from our `App` component and display them in our app. Finally, we will add a CSS framework (Semantic UI React) and style our application a bit.

The first thing we want to do is create a `Cypress` test based on that screenshot.

`$ touch cypress/integration/userCanSeePortfolioStructure.feature.js`

```js
// cypress/integration/userCanSeePortfolioStructure.feature.js

describe('Portfolio interface', () => {
  it('successfully renders',() => {
    cy.visit('http://localhost:3000');
    cy.get('#header').should('contain', 'My Portfolio');
    cy.get('#footer').should('contain', 'Made with React 16.12.0');
    cy.get('#hello').should('contain', 'Hello World');
  })
})
```

That is our test, let's get to writing some code.
Let's start by creating two new files in our `src` folder. `Footer.jsx` and `Header.jsx`. In the first iteration, we can just add some basic text. We WILL style them in a moment.

This is the code for `Header.jsx`:

```js
// src/Header.jsx

import React from "react";

const Header = () => {
  return (
    <nav>
      <h1 id="header">My Portfolio</h1>
    </nav>
  );
};

export default Header;
```

And this is how we will define the component in `Footer.jsx`:

```js
// src/Footer.jsx

import React from "react";

const Footer = () => {
  return (
    <footer>
      <h1 id="footer">Made with React {React.version}</h1>
    </footer>
  );
};

export default Footer;
```

Consequently, we want to change the markup in our `App` component to include and reference our new components:

```js
// src/index.js

import React from "react";
import ReactDOM from "react-dom";
import Hello from "./Hello";
import Header from "./Header";
import Footer from "./Footer";

const App = () => {
  return (
    <div>
      <Header />
      <Hello />
      <Footer />
    </div>
  );
};

ReactDOM.render(<App />, document.getElementById("app"));
```

Now, restart your development server to see the new components in action. They should be visible, but not impress you a lot. If you run the `Cypress` test everything should go green. Right? It's time to add some styling to our application.



## Adding a CSS framework

We will make use of [SemanticUI](https://semantic-ui.com/introduction/getting-started.html) to add a look and feel to our application.

Let's start by adding SemanticUI as a dependency first, then do a bit of configuration, and finally, add some SemanticUI classes to our components.

In this project, we will add Semantic UI to our project by using CDN and referencing the necessary files from our `index.html` file. Please note that this method is okay at this stage (for educational purposes during), but not desirable when working on projects that will actually be deployed to production servers. But more on that later.

Please modify the `<head>` tag of your `index.html` file to look like this:

```html
<!-- index.html -->

<head>
  <meta charset="UTF-8" />
  <meta
    name="viewport"
    content="width=device-width, initial-scale=1, shrink-to-fit=no"
  />
  <meta
    http-equiv="Content-Security-Policy"
    content="default-src *; connect-src * ws://* wss://*; style-src * 'unsafe-inline' 'unsafe-eval'; media-src * ; img-src * data:; font-src * ; script-src * 'unsafe-inline' 'unsafe-eval';"
  />
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css"
  />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.js"></script>
  <title>Portfolio</title>
</head>
```

This will make Semantic UI version 2.4.1 available in your project.

## Styling

Semantic UI comes with a LOT of predefined classes and can be a bit overwhelming. While writing this documentation, I did spend some time on [CodePen](https://codepen.io/) looking for some good UI examples to look at. I came up with the following styling that you are welcome to use (You can, of course, give your portfolio your own look and feel by combining the available classes on your own, or write some on your own).

First, we need to add a `<style>` tag to our `index.html` (Don't worry, we will extract that to a separate file in a moment.). Please add the following inside your `<body>` tag:

```html
<style>
  footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 60px;
    padding-top: 20px;
    padding-bottom: 20px;
    background-color: #f5f5f5;
    margin-top: 0;
    margin-bottom: 0;
    text-align: center;
  }

  .main.container {
    margin-top: 4em;
  }
</style>
```

Now, we will have to make use of the Semantic UI classes and our own custom css in our components. We do that by modifying the `return` of our components with the following markup examples:

This is the code for the `return` in `Header.jsx`:

```js
// src/Header.jsx

return (
  <nav className="ui fixed inverted menu">
    <div className="ui container">
      <h3 id="header" className="header item">My Portfolio</h3>
    </div>
  </nav>
);
```

And for `Footer.jsx`:

```js
// src/Footer.jsx

return (
  <footer>
    <div className="ui container">
      <p id="footer">Made with React {React.version}</p>
    </div>
  </footer>
);
```

And for `Hello.jsx`:

```js
// src/Hello.jsx

return (
  <div className="ui main container">
    <h1 id="hello">Hello World</h1>
  </div>
);
```

If you restart your development server you should be able to see the following: ![](https://github.com/CraftAcademyLabs/ca_course/raw/master/week3/portfolio_challenge/assets/portfolio_v2_hello_world.png)

## Extract custom css to a file

In the example above, we are making use of a mix of Semantic UI classes and custom css. We added our own css in as a `<style>` tag directly into our `index.html` markup. That is not a good practice. After a while, that tag can grow rather big and cumbersome to maintain and read.

There are plenty of different ways we could handle this. For now, we will go with the least complex solution and move the css into a separate file. It does not solve all the problems for us but will do for now.

Create a new file called `style.css` in the `dist` folder and move your custom css to that file. Modify the `<head>` of your `index.html` to include that file. Make sure to add the `<link` AFTER the one that pulls in Semantic UI from CDN.

```html
<link rel="stylesheet" href="dist/style.css" />
```

## Wrap up

In this section, we started to set up the structure of our application by creating a few new components and displaying them on the page. We also added a CSS framework, gave our components a look and feel and made sure that we can reuse the classes. That's good progress - we are getting somewhere.

In the next part, we will look into navigating within our portfolio (we will add an "About Me" and "My Projects" sections).
