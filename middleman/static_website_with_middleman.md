## Static Website with Middleman

Earlier in the course, you had a first run at building a website. Okay, it was a just some a basic HTML page. But the aim of that assignment was to give you a refresher of some basic HTML and CSS. In this section, we will take things up a notch a little bit. We will introduce you to [Middleman](//middlemanapp.com), a tool that will allow you to build *Static Websites*. This will allow us to use some software development best practices to build our sites.


### What is Middleman?

> Middleman "is a static site generator using all the shortcuts and tools in modern web development"
>
> <cite>[Middleman site](https://middlemanapp.com/)</cite>

Static websites are widely used because they are fast, easy to setup. They are served to the user exactly as stored and there is no transaction to a database. It's pretty much HTML, CSS and JavaScript if needed.

You might be wondering now, why go through the trouble of using a tool like Middleman to build a static website?

Building a static site with many pages can lead to having lots of repeated code across. Updating them during development will quickly become a nightmare. Middleman will allow you to structure your project in a modular way using things like layouts, partials, etc. Giving you a DRY and more manageable project.

Middleman uses the Ruby programming language. But don't worry if you're still new to Ruby as you'll likely not write too much of it while build your site.

Now let's [install middleman and build our first site](setup_middleman.md).
