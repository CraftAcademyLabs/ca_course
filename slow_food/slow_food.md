### SlowFood (Sinatra)

Our client is a Restaurant owner that needs an website where he can list his menu and allow visitors to place orders.

#### Scope (v1)

The first version of the application has limited functionality

* The owner need to access a protected part of the application to set up information about his Restaurant and his Menu
* Menu needs to consist of many Dishes
* Each Dish is a Starter, Main course or Dessert
* Visitors of the site can add Dishes to an Order
* Order need to calculate a Total price and a Pick up time (30 minutes)

Nothing else should be considered or implemented.

#### Tools
* Sinatra as web framework
* Foundation6 as CSS framework
* PostgreSQL for database
* Warden for user authentication
* Cucumber for acceptance tests
* RSpec for unit tests


We have set up a repo for the project at https://github.com/CraftAcademy/slow_food_sinatra

Fork that repo and go over the code and gems already included in the Gemfile. Your very fists assignment is the get that code working. **Make use of all the previous challenges including the Workshop to find answers to obstacles you might encounter.**

After that you need to create some User stories to cover the above feature requirements. 


Next, I want you to draw an ERD (Entity Relationship Diagram) of the system the way you see it fit. ER-modeling is a data modeling technique used in software engineering to produce a conceptual data model of a information system. Diagrams created using this ER-modeling technique are called Entity-Relationship Diagrams, or ER diagrams or ERDs. So you can say that Entity Relationship Diagrams illustrate the logical structure of databases.

There are three basic elements in ER-Diagrams:

Entities are the "things" for which we want to store information. An entity is a person, place, thing or event.
* Attributes are the data we want to collect for an entitiy.
* Relationships describe the relations between the entities.
* ERDs show entities in a database and relationships between tables within that database. It is essential to have ER-Diagrams if you want to create a good database design. The diagrams help focus on how the database actually works.
* 
You can use this ERD Tool: https://editor.ponyorm.com/


