---
title: "SlowFood Challenge"
subtitle: "Software As A Service Introduction"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Internationalization, Localization, Rails"
keywords: [Internationalization, Localization, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

## SlowFood Challenge

This weeks challenge is to build an Online Food Ordering System - a web-based application that allows customers to order their food for home delivery, or for pick up from the restaurant. Our imaginary client is a restaurant owner that needs a website where he can list his menu and allow visitors to place orders. 

This is the first challenge touching upon the e-commerce domain that we will be working on during the course. **The purpose of this challenge is to simulate a real project and prepare you for the Mid Course Project, that you will be working on in week 6 of the Bootcamp.**

This challenge introduces a new set of technologies and skills you'll need as a developer. The main objective for this challenge is to practice the workflow that we want to use in our projects with a focus on:

* Agile methods for software development
* Pair Programming
* Collaboration using Git and GitHub
* Test Driven Development and Behavior Driven Design


### Learning Objectives
We set the goals high for this week. Apart from programming skills, that are of course in focus at all time, we will be working in teams during this week. Set yourself up to practice and learn more about:

* Use basic Agile practices
  - self organizing teams
  - iterative and incremental design*
  - the importance of planning your work
* Learn about Ruby on Rails
  - understand the concept of ORM's vs SQL (ActiveRecord)
  - understand the MVC structure
    - controllers
    - routes
    - models
    - relation between models
    - concept of `params` (See: https://www.youtube.com/watch?v=y57OnWV6dRE)
* Learn more about User Authentication
* Learn about the hardships that will occur if we can't read documentation for libraries we use in our projects (gems).
* Learn more about Test- and Behavior Driven Development or Acceptance - Unit Test cycle.
* Really embrace and understand the benefits of Pair Programming && collaboration using Git and GitHub
* Learn about deployment to Heroku and the benefits of services like Heroku, DigitalOcean and AWS.

**Please note that there may be subtle errors or typos in the following materials. Please try to approach those as challenges on which to polish your debugging and problem solving skills**

\*
- **Incremental** – adding new functionality in small chunks.
- **Iterative** – performing repeatedly, i.e. adding new functionality in a repetitive or cyclic manner.

## Tools
During this project, you will be using a variety of tools. You have used some of them in other challenges, but many will be new to you.

* [Ruby on Rails](https://rubyonrails.org/) as web framework
* [Rails 5.2 Boilerplate](https://github.com/CraftAcademy/boilerplate) A scaffolded application with a basic setup for testing.
* [Cartify](https://github.com/CraftAcademy/cartify) a gem for order functionality and check out flow 
* [Devise](https://github.com/plataformatec/devise) for user authentication and account management
* [Tailwind CSS](https://tailwindcss.com/) as CSS framework
* [PostgreSQL](http://www.postgresql.org/) as a database with ActiveRecord as ORM

* [Cucumber](https://cucumber.io/) for Acceptance tests
* [RSpec](http://rspec.info/) for Unit tests
* [Pony](https://editor.ponyorm.com/) for creating end editing Entity-Relationship Diagrams (optional)
* [GitHub](https://github.com/) to store your code and make it available for the entire team
* [Pivotal Tracker](https://www.pivotaltracker.com/) as Project Management tool for tracking Features, Chores, and Bugs.

As you can see, there's plenty of tools and services for you to familiarize with.

## Scope

In the first version of the application should focus on the following functionality:

* The restaurant owner needs to access a protected part of the application to set up information about his restaurant (like address, opening hours, etc) and his menus (products ordered in categories)
* A menu needs to consist of many dishes
* Each dish has/belongs to a Category. It is either a 'Starter', a 'Main course' or a 'Dessert'
* The restaurant also sells cold beverages and side orders like salads, bread, etc.
* Visitors to the site can add various products to their order
* In order to finalize an order (Check out) a customer needs to become a registered user
* Order need to calculate a total price and a pickup time (30 minutes)

**Nothing else should be considered or implemented.**




 






