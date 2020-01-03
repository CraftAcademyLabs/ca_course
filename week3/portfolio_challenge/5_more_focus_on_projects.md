## More focus on projects - cards

We will spend some more time on our Project display and learn about `props`

This is our end game for this section of the course:

![](https://github.com/CraftAcademyLabs/ca_course/raw/master/week3/portfolio_challenge/assets/portfolio_v2_project_cards.png)

We need to add some more content to our `projects.json` and describe our projects in more detail.

```json
[
  {
    "id": 1,
    "name": "My First Website",
    "image": "http://www.4president.us/websites/2000/2000w/gore2000home.gif",
    "description": "This was my first project. The guy lost, but won the popular vote!"
  },
  {
    "id": 2,
    "name": "UI Design",
    "image": "https://images.unsplash.com/photo-1522542550221-31fd19575a2d?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1950&q=80",
    "description": "Designing user interfaces is fun. I want to learn more about that..."
  },
  {
    "id": 3,
    "name": "Mobile UX",
    "image": "https://images.unsplash.com/photo-1534237886190-ced735ca4b73?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1950&q=80",
    "description": "I like to design for the mobile platform. The challenges to build UI's for smartphones is challenging but extremely rewarding."
  }
]
```

If you examine the object carefully, you'll notice that we've added keys for `image` and `description`. Feel free to add your own content if you like, or use the example above.

## Props

React components are reusable and can be used over and over again in the UI. Quite often we want to modify what data the component we use is going to display. We can pass in data to a component using `props`.

Props (short for **properties**) are equivalent to parameters in vanilla JavaScript and used to send data to components. The main difference between `state` and `props` is that props are immutable - they can not be changed.

We will make use of props in our projects list.

## Re-styling the projects list.

In the previous part, we created a `Projects` component. I suggest that we modify the `render()` function with the following code where we delegate the rendering of each project to a new component (that we will create in just a minute) called `ProjectCard`. The reason for this is that I plan to add more information and styling to each project and want to keep the `Project` component clean. And, I want to showcase how we can use `props`- this is a course after all, and we came here to learn, right?

Modify the part of the code where we populate the `projectsList` variable with HTML.

```js
// src/Projects.jsx

if (projects.length > 0) {
  projectsList = projects.map(project => {
    return (
      <div key={project.id}>
        <ProjectCard project={project} />
      </div>
    );
  });
}
```

And modify the JSX return:

```js
return (
  <div className="ui main container">
    <h1 className="ui header">My Projects</h1>
    <div className="ui stackable four column grid">{projectsList}</div>
  </div>
);
```

We also need to create and `import` the `ProjectCard` component into `Projects`:

```
$ touch src/ProjectCard.jsx
```

```js
import ProjectCard from "./ProjectCard";
```

Please take a close look at `<ProjectCard project={project} />`. What we are doing is sending the current `project` object to our new `ProjectCard` component. It will be accessible as `this.props.project`.

Our new component can have a static type, so we do not have to use `class`. Create a new file in the `src` folder and call it `ProjectCard.jsx`. Add the following code to the new file:

```js
import React from "react";

const ProjectCard = ({ projects }) => {
  return (
    <>
      <div class="ui card">
        <div class="image">
          <img src={project.image} />
        </div>
        <div class="content">
          <h3 class="ui header">{project.name}</h3>

          <div class="description">{project.description}</div>
        </div>
      </div>
    </>
  );
};

export default ProjectCard;
```

There are a few things to look out for.

Pay attention to the `props` parameter we are receiving. It's the `project` object we sent off from the `Projects` component. We populate our HTML with data from that object (i.e. `project.name`, `project.description`, etc.)

## Wrap up

By adding a new component we can keep the code cleaner and more readable. It is also possible for us to reuse the component in other parts of the application. Can you think of any other use case for the `ProjectCard`? Does the naming hinder you from thinking of one?
