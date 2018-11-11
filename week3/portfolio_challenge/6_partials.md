## Partials
Partials are a way of sharing content across pages and helps you to keep your code DRY. They can be used both in templates and in layouts.

Let's assume that we want to display a list of projects on our page.

In order to group our `partials`, we want to create a partials folder in our `source`folder.

In that folder, let's create a file called `_projects.html.haml`. **Note the underscore at the beginning of the filename!**
```shell
# source/partials/_footer.html.haml

%div.projects
  %h3 My Portfolio
  %p Built using the awesome Middleman framework
```
Under the `= yield` command in your main layout file, place the call to render the projects partial.
```shell
# layouts/layout.html.haml

= partial 'partials/projects'
```
If you run your specs now, the one we just added should go green.

**Knowing this, you can add a more complex partial to your application and keep your code DRY**

![modified_middleman_landing_page](/static/modified_middleman_landing_page.png)

*Figure: Still not much to show the world but with some more content...*
