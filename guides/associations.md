# Intro to Associations in Rails

In this guide we will cover what associations are and what they are most commonly used for.

### What are associations?
Associations are the link between two different ActiveRecord Models in Ruby on Rails.

This means that associations give us the ability to connect two different database models with a simple line of code. We use them to make our coding easier rather than creating multiple models when we could use the ones we already have and have them connected. Which is basically the reason we use ORM database model.

What does this mean in practical terms?

Let’s illustrate this with an example:

Imagine that we have two different models (a.k.a database tables):

The “Authors” model and the “Books” model. In the Author model we have a lot of authors that have written books many books, and respectively in the “Books” model we have a lot of books that are written by the Author from the Author model. We would like to have two different database models for these because otherwise the database model would become to large and hard to handle.

How would we create a link between these models?

Rails takes care of this for us behind the scenes. The only thing we need to do is to define their relationship through a few simple lines of code with ActiveRecord Associations.

Before we can start showing how to make these connections we need to talk about different association types. There are basically three main association types One-to-One ( 1:1 ), One-to-Many ( 1:M ) and Many-to-many (M : M). Let’s go through these one by one.


**One-to-One ( 1:1 )**


![](https://docs.google.com/a/craftacademy.se/drawings/d/sfQAXpycOUZvZGcRw0iIeXA/image?w=206&h=36&rev=1&ac=1)

One to one associations is easily explained by an example. Imagine that we have a teacher. This teacher teaches only from one classroom, the subject that the teacher teaches could be different but the teacher stays in the classroom for all of those.


We would call this relationship for one-to-one relationship a teacher only works from one classroom. We would say that the teacher `belongs_to` the classroom and the classroom `has_one` teacher. We would say that the classroom is the parent table and the teacher is the child table


Now we could argue that the teacher `has_one` classroom and the classroom `belongs_to` a teacher. And we could easily swap these two around and the connection would stay the same, but this is a design choice that we are doing because it makes more sense because we see the classroom as a fixed resource while teachers can come and go.


**One-to-Many ( 1 : M )**



![](https://docs.google.com/a/craftacademy.se/drawings/d/swfhjeL6Z030oQ__HbM8DnQ/image?w=181&h=99&rev=1&ac=1)



We use one_to_many associations when we want to connect a single model to multiple models.

There are several use cases for this:



Let’s illustrate this with the teacher example.



“A teacher has multiple courses to teach”. One teacher in an highschool would certainly have multiple courses to teach her/his students. Like maths, biology and programming. To link the courses to the teacher we would use the one-to-many association. This is the most commonly used association in rails. We would say that the teacher `has_many` courses and that the individual courses `belongs_to` the teacher


The “Teacher” model is the parent table and all the “Courses” models are the child tables.


**Many-to-Many ( M : M )**



![](https://docs.google.com/a/craftacademy.se/drawings/d/sd6OTYyno2LD9GHGj9YZWAQ/image?w=204&h=114&rev=26&ac=1)



Many-to-many associations is the hardest ones to get a grip on. Because we have a many to many relationship and each of them belongs to the other how could we decide what the parent and the child tables are? This would create problems for us no matter what we would do. And also imagine that we have a lot of tables for example thirty students take biology, they also take maths, and programming.



But a school has more than thirty students so they then there would be more students taking different courses. How would we set up this relationship?



The traditional way to solve this by using a join table, that would house the relationship. But because this is rails all this functionality is handed to us in an easy to manage way. We would say that a course `has_many` students and `belongs_to` students. From the reverse side we would say that a student `has_many` courses and also belongs_to courses.


### Foreign Key


Now what actually happens in the database tables that allow us to make the connections?

The answer is the foreign keys.



“A foreign key is a [field](https://en.wikipedia.org/wiki/Field_(computer_science)) (or collection of fields) in one [table](https://en.wikipedia.org/wiki/Table_(database)) that uniquely identifies a row of another table or the same table”



Kind of confusing right? Let’s illustrate this with another example:



A table called Employees has a primary key called `employee_id`.



Another table called Employee Details has a foreign key which references `employee_id` in order to uniquely identify the relationship between the two tables. This means that that the foreign key shows us what employee has what employee detail by connecting the two tables through an unique id.



The foreign key is added to the child table. In our case this means in whatever class we have the `belongs_to` association, that is where we will add the foreign key.


## How to set up One-to-one associations

There are two main use cases for setting up one-to-one associations



The first use case is when we have unique objects that you could only have one of: for example

```ruby

employee has_one :office

```
or

```ruby

student has_one :id_card

```

This is a good reason to use one-to-one associations.



The second t use case is when we want to split up a single table into two tables. For example if we have a table with :customer information and we would like to break out the :billing_address for some reason then that would also be a use case for one-to-one associations.



Usage:

```ruby

class Classroom < ApplicationRecord
	has_one :teacher
end

```


```ruby

class Classroom < ApplicationRecord
	belongs_to :classroom
end

```
## How to set up One-to-Many associations

One-to-many associations are the most widely used associations in rails.

It is important to remember that child object is returned as an object. When setting up the association we need to make the belongs_to objects plural. Because we have many objects that belongs to the parent table.

It’s important to remember that Rails has a built in validation for the belongs_to object. This means that you can not save the object unless it belongs to a parent table. The validation is in place so that orphaned objects should not exist in the database and lowering it’s performance.


```ruby
class Photographer < ApplicationRecord
	has_many :photos
end
```



```ruby
class Photo < ApplicationRecord
	belongs_to :photographer
end
```
## How to set up Many-to-Many associations


Many-to-many association requires a little more work. Apart from setting up the associations we also need to generate a [join table](http://edgeguides.rubyonrails.org/active_record_migrations.html#creating-a-join-table) where we can house the foreign keys.


```ruby
class Student < ApplicationRecord
	has_and_belongs_to_many :courses
end
```


```ruby
class Courses < ApplicationRecord
	has_and_belongs_to_many :students
end
```

This concludes our intro to associations. To learn more about associations I recommend you to read the official rails [documentation](http://edgeguides.rubyonrails.org/association_basics.html)
