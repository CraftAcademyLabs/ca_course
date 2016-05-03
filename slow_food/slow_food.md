### SlowFood (Sinatra)

Our client is a Restaurant owner that needs a website where he can list his menu and allow visitors to place orders.

#### Scope (v1)

The first version of the application has limited functionality

* The owner need to access a protected part of the application to set up information about his Restaurant and his Menu
* A Menu needs to consist of many Dishes
* Each Dish has a Category. It is either a Starter, a Main course or an Dessert
* Visitors of the site can add Dishes to an Order
* In order to finalize an Order (Check out) a Visitor needs to become a Registered User
* Order need to calculate a Total price and a Pick up time (30 minutes)

**Nothing else should be considered or implemented.**

#### Tools
During this project you will be using a variety of tools. You have used some of them in other challenges, but many will be new to you. 
* [Sinatra](http://www.sinatrarb.com/) as web framework
* [ZURB Foundation6](http://foundation.zurb.com/sites/docs/) as CSS framework
* [PostgreSQL](http://www.postgresql.org/) as a database with DataMapper as ORM
* [Warden](https://github.com/hassox/warden) for user authentication
* [Cucumber](https://cucumber.io/) for Acceptance tests
* [RSpec](http://rspec.info/) for Unit tests
* [Pony](https://editor.ponyorm.com/) for creating end editing Entity-Relationship diagrams
* [GitHub](https://github.com/) to store your code and make it available for the entire team
* [Waffle.io](https://waffle.io/) as Project Management tool for tracking features, issues, bugs, etc.

The purpose of this exercise is to simulate a real project and prepare you for the mid course project that you will be working on in week 6 and/or 7 of the Bootcamp.  


We have set up a repo for the project at https://github.com/CraftAcademy/slow_food_sinatra

**Remember, the code you get to start with is not necessarily right. It is up to you to make this work.**

Fork that repo and go over the code and gems already included in the Gemfile. Your very fist assignment is the get that code working. **Make use of all the previous challenges including the Workshop to find answers to obstacles you might encounter.**

After that you need to create some User stories to cover the above feature requirements. 


Next, I want you to draw an ERD (Entity Relationship Diagram) of the system the way you see it fit. ER-modeling is a data modeling technique used in software engineering to produce a conceptual data model of a information system. Diagrams created using this ER-modeling technique are called Entity-Relationship Diagrams, or ER diagrams or ERDs. So you can say that Entity Relationship Diagrams illustrate the logical structure of databases.

There are three basic elements in ER-Diagrams:

Entities are the "things" for which we want to store information. An entity is a person, place, thing or event.
* Attributes are the data we want to collect for an entity.
* Relationships describe the relations between the entities.
* ERD's show entities in a database and relationships between tables within that database. It is essential to have ER-Diagrams if you want to create a good database design. The diagrams help focus on how the database actually works.
* 
You can use this ERD Tool: https://editor.ponyorm.com/






