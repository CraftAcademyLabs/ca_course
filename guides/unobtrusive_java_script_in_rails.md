---
title: "Unobtrusive JavaScript in Rails"
subtitle: "Part 1 - Introduction to SPA Functionality In Rails (With A Twist)"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "JavaScript, Rails"
keywords: [JavaScript, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Introduction to Rails’ unobtrusive JavaScript adapter

Let's start with taking a look at RailsUJS (`rails-ujs`) - the unobtrusive scripting adapter in Ruby on Rails. This adapter brings a lot ou useful functionality to your presentation layer: your view-templates, but also how you can navigate within your app. 

The following features are achieved by adding `data` attributes to your HTML markup. In Rails, they are added by the framework's template helpers.

* force confirmation dialogs for various actions
* make non-GET requests from links
* make forms or hyperlinks submit data asynchronously with Ajax
* have submit buttons and links automatically disabled on form submit to prevent double-clicking
* and more...

* A form is submitted via a AJAX call and received by the controller.
* Server creates or updates an object.
* Server generates a JavaScript response that includes the updated HTML template for the model.
* Client evaluates the JavaScript returned by the server, which then updates the Document Object Model

# The basics - `remote: true`

The RailsUJS gives us an extrimly simple inteface to enable AJAX calls on any link. 

Using the `link_to` helper, 

```erb
<%= link_to "Add post", new_post_path, remote: true %>
```
Or as I prefer to write it using HAML:

```haml 
= link_to "Add post", new_post_path, remote: true
```

That will render into: 

```html
<a href="/posts/new" data-remote="true">Add post</a>
```

As a matter of fact, if you don't want to use helpers, or if you are using RailsUJS outside of Rails, you can write the above directly in your html markup.

## The format of the request

The big difference with this link is, of course the type of the request format. This is really important, so let us take a closer look at what is happening. 

Let's say that your view has a link 







