## Databases, ORM's, etc.
### What are ORM's?
Object-relational mapping (ORM, O/RM, and O/R mapping tool) is a technique that connects the rich objects of an application to tables in a relational database management system. Using ORM, the properties and relationships of the objects in an application can be easily stored and retrieved from a database without writing SQL statements directly and with less overall database access code.



### Explain the difference between a `has_one` and `belongs_to` association
`has_one`: Indicates a direct 1:1 relationship between objects where each instance of a model contains one instance of another model.

![](https://process.filestackapi.com/Ax6CqCJ04Sg6MvuwElEWLz/resize=width:600/http://guides.rubyonrails.org/images/has_one.png)

<small>Source: Rubyonrails.org</small>

`belongs_to`: Represents the inverse of a `has_one` (or `has_many`) association.

![](https://process.filestackapi.com/Ax6CqCJ04Sg6MvuwElEWLz/resize=width:600/http://guides.rubyonrails.org/images/belongs_to.png)
<small>Source: Rubyonrails.org</small>

- A good way to remember this is that if a table has foreign keys, its model should have a `belongs_to` association.

- Be able to explain the difference and describe an example of how you would assign these associations to two related models.

### Explain the difference between a `has_many, through:` and `has_and_belongs_to_many` association

### Explain a polymorphic association
Polymorphic associations can be tricky to understand and many times one can get confused about when to use them, how to use them and how they are different from other associations.

Polymorphic associations allow a model to belong to more than one other model through a single association.

In the example below the model `TeachingAssistant` is associated with two models; `Course` and `Lab` in single association via `ta_duty` which is helping in achieving the association.
```shell
$ rails g model TeachingAssistant name:string ta_duty_id:integer ta_duty_type:string
```
The `ta_duty_id` is a foreign key and `ta_duty_type` will tells the model `TeachingAssistant` which model it is associated with.

```ruby
class TeachingAssistant < ActiveRecord::Base
  belongs_to :ta_duty, polymorphic: true
end
```

```ruby
class Course < ActiveRecord::Base
  has_many :teaching_assistants, as: :ta_duty
end
```

```ruby
class Lab < ActiveRecord::Base
  has_many :teaching_assistants, as: :ta_duty
end
```

```shell
:001 > ta = TeachingAssistant.create(name: 'Thomas')
:002 > course = Course.create(name: 'CA PreppCourse')
:003 > ta.update_attribute(:ta_duty, course)
 => true
:004 > Course.last.teaching_assistants.last.name
 => "Thomas"
```
