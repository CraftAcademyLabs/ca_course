### SlowFood (Sinatra)

**Welcome to the Week 3 of the Bootcamp and the Slow Food Challenge. Our client is a Restaurant owner that needs a website where he can list his menu and allow visitors to place orders. The challenge is to build an Online Food Ordering System - a web based application that enables customers to order their food online for home delivery or pick up from the restaurant.** 

This is the first challenge in the Online Food Ordering domain that we will be working on during the course. It constitutes a part of a larger theme that we are using throughout our bootcamp. Further down the road we will challenge you with developing a more extensive application that will have more of a marketplace functionality. The idea is that by building a fully functional service with multiple restaurants, delivery system, payments, ranking, etc, you will learn how to build rather complex systems and learn to use different technologies. Eventually you will be challenged with building an API to expose the applications data to other applications (like a mobile client).

**This week we will start with building a Minimum Viable Product using Sinatra with limited functionality (see the Requirements section).**

This challenge introduces a new set of technologies and skills you'll need as a developer. The main objective for this challenge is to practice the workflow that we want to use in our projects with focus on:

* Agile methods for software development
* Pair Programming
* Collaboration using Git and GitHub
* Test and Behavior Driven Development


#### Tools
During this project you will be using a variety of tools. You have used some of them in other challenges, but many will be new to you. 
* [Sinatra](http://www.sinatrarb.com/) as web framework
* [ZURB Foundation 6](http://foundation.zurb.com/sites/docs/) as CSS framework
* [PostgreSQL](http://www.postgresql.org/) as a database with [DataMapper](http://datamapper.org) as ORM
* [Warden](https://github.com/hassox/warden) for user authentication
* [Cucumber](https://cucumber.io/) for Acceptance tests
* [RSpec](http://rspec.info/) for Unit tests
* [Pony](https://editor.ponyorm.com/) for creating end editing Entity-Relationship diagrams
* [GitHub](https://github.com/) to store your code and make it available for the entire team
* [Waffle.io](https://waffle.io/) or [ZenHub.io](https://www.zenhub.io/) as Project Management tool for tracking features, issues, bugs, etc.

**The purpose of this exercise is to simulate a real project and prepare you for the mid course project that you will be working on in week 6 and/or 7 of the Bootcamp.**  

#### Scope

The first version of the application has limited functionality

* The owner need to access a protected part of the application to set up information about his Restaurant and his Menu
* A Menu needs to consist of many Dishes
* Each Dish has a Category. It is either a Starter, a Main course or an Dessert
* Visitors of the site can add Dishes to an Order
* In order to finalize an Order (Check out) a Visitor needs to become a Registered User
* Order need to calculate a Total price and a Pick up time (30 minutes)

**Nothing else should be considered or implemented.**




 






