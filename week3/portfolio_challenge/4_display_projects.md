## Display your projects

In this section, we will focus on the `My Projects` display. We will take a closer look at how we can fetch some data (about projects) in JSON format, pass that data in to the `render()` function, and display it in the browser. 

Until now, we've only created stateless components (stored in functions). Now, we need to unleash some other powers that come in React. Components defined as classes are stateful, and provide more features that will be useful to us. To define a React component class, you need to extend `React.Component` (or simply `Component` if you import it as a module - more on that below.). 

## Extending React.Component vs creating a function

Take a closer look at these 2 examples:

Using `const`:
```javascript
const CustomComponent = () => {
  return (
      <CustomElement />
  )
};
```

Using `class`:
```javascript
class CustomComponent extends Component {
  render() {
    return (
      <CustomElement />
    )
  }
}
```
What is the difference? Well, if you need to use any lifecycle events or set any state you should use class. What are lifecycle events? Let's take a look at that.

## Component lifecycle events

As mentioned above, components can be defined as classes or functions. The methods that you are able to use on these are called lifecycle events. These methods can be called during the lifecycle of a component, and they allow you to update the UI and application state.
**Mounting**, **Updating**, and **Unmounting** are the three phases of the component lifecycle. 

### Mounting
Mounting phase is when an instance of a component is being created and inserted into the DOM. Apart from the `constructor()`, we have access to two lifecycle methods in this phase: `componentWillMount`, and `componentDidMount`.

* **`constructor()`** is the first function called upon instantiating a React class and is where the initial state of the component is being set.


* **`componentWillMount`** is called only once in the component lifecycle, immediately before the component is rendered (marked for deprecation in future releases of the library, please use `componentDidMount` below instead).
*  **`componentDidMount`** is also only called once, but immediately after the render() method has taken place. That means that the HTML for the React component has been rendered into the DOM and can be accessed if necessary.  

### Updating
Anytime a component is updated or state changes then it is rerendered. These lifecycle events happen during updating in this order.

* **`static getDerivedStateFromProps`** enables a component to update its internal state as the result of changes in props.
* **`shouldComponentUpdate`** allows your Component to exit the Update life cycle if there is no reason to apply a new render.
* **`render`** create elements (generally via JSX) and return them. Unlike any other method in the Life Cycle, `render()` is the one method that exists across multiple life cycle phases. It occurs in Mount as well as in Update phases.
* **`componentDidUpdate`** called every time a re-render has been performed, such as when `this.setState()` is called. 

### Unmounting
The unmounting (or deletion, or "cleanup") phase of the life cycle, called when a component is being removed from the DOM. During this phase `componentWillUnmount` is the only lifecycle event we can call.

Now that we have more knowledge about the lifecycle events, we can move on with building our application.

## Projects in JSON

We will make use of the `constructor()` and `componentDidMount` event in our implementation in order to display information about our projects. For that reason, we need to refactor our `Projects` component from a stateless component and use `class`. For starters, let's make the following changes to `Projects.jsx`. We will go over what is going on in the code 

```javascript
import React, { Component } from "react"

class Projects extends Component {
  constructor() {
    super();
    this.state = {
      projects: [
        {
          "id": 1,
          "name": "My First Website"
        },
        {
          "id": 2,
          "name": "FizzBuzz"
        }
      ]
    };
  }

  render() {
    const projects = this.state.projects
    let projectsList

    if (projects.length > 0) {
      projectsList = projects.map(project => {
        return (
          <div key={project.id}>
            <h3 className="ui header">
              {project.name}
            </h3>
          </div>
        )
      })
    }


    return (

      <div className="ui main container">
        <h1 className="ui header">My Projects</h1>
        {projectsList}
      </div>

    )
  }
};

export default Projects
```
What we in the code above is the following:

1. We change the component from a stateless function to a stateful class.
2. We are setting the initial state for projects to hold a simple JSON object that describes our projects. We give each project an `id` and `name` key with values
3. We modify the `render()` function and:
    * We save `this.state.projects` in a constant (`projects`)
    * We declare a new variable named `projectsList`(we will store some HTML in that variable in a moment)
    * We iterate over `projects` using `.map` function and create some HTML that holds a `key` and the name of the project wrapped in a `<h3>` tag that we store in `projectsList`.
    * And finally, we render some HTML and make use of the `projectsList` in the `return` - that will be what the component will look like in our UI. 

The `key` calls for a closer explanation. A `key` is a special string attribute you need to include when creating lists of elements. Keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a stable identity. In our case we use the value of the projects `id` to set the key. 

## Fetching data from a file

Having information about our projects hard coded in the components `constructor()` does not feel good. Imagine if the project information would consist of a more complex dataset? We will take another approach and store the project information in a file and fetch it into our `Projects` component. We will use a external library called [`axios`](https://www.npmjs.com/package/axios) to read a JSON file that we will create in a new subfolder to the `src` folder that we will call `data`. [Axios](https://www.npmjs.com/package/axios) is a library and we need to add it using NPM. The JSON file will look like this:

```json
[
  {
    "id": 1,
    "name": "My First Website"
  },
  {
    "id": 2,
    "name": "FizzBuzz"
  }
]
```

## Execution

```bash
$ npm i -S axios
//
$ yarn add axios
$ mkdir src/data
$ touch src/data/projects.json
// add the json code to src/data/projects.json
```

Now, we need to modify our `Projects` component again. 

The first thing we need to do is to make the `axios` library available to us:

```javascript
import axios from "axios"
```
Next, remove the projects data from the `constructor()` (we will use `this.setState()` function in just a moment to populate it.)

```javascript
constructor() {
  super();
  this.state = {
    projects: []
  };
}
```

Now it's time to get one of those lifecycle events to work for us. We will tap into the `componentDidMount()` event and use `axios` to read our JSON file and set the state of the component with projects data. 

Add the following function to the component:

```javascript
componentDidMount() {
  axios.get('./src/data/projects.json')
    .then(response => {
      this.setState({
        projects: response.data
      })
    })
}
```

That should do it. We do not have to make any changes to the `render()` function. Now restart your development server and you should be able to see the projects list if you click on the `My Projects` list. 

## Wrap up

There's a lot more information about our projects that we would like to display on our page and further refactorings we can make to the code to have it more readable. But we'll deal with that in the next section of the course. 









