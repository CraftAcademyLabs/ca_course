## General Ruby Knowledge

### Explain how almost everything is an object in Ruby
This is a simple question based on complex concept. Here’s your chance to show off your theoretical knowledge and demonstrate that you can have an in depth conversation on class hierarchies, inheritance, methods, encapsulation, polymorphism, and more.

Explaining this could take an hour or a few minutes – there’s no single correct answer here, save from being able to demonstrate your familiarity with OOP concepts.

### What's your favorite testing tool?
The specific answer here is probably not important in and of itself – What’s important is that you can demonstrate familiarity with at least several testing tools, and be able to discuss their individual advantages and weaknesses. Never ventured outside of Rails default testing tools? Take some time to familiarize yourself with tools such as Rspec, FactoryGirl, Capybara, and Cucumber.

### What are Gems and which are some of your favorites?
Short answer: Gems are packaged bits of Ruby code that you can install to extend or add functionality to your app.

Be sure to be able to discuss a list of your favorite gems, why you like them, and any customizations you like to add. This is also a good opportunity to highlight any gems you may have published.

### What is a class?
A text-book answer: *"classes are a blue-print for constructing computer models for real or virtual objects..."*

A better answer could be: *"classes hold data, have methods that interact with that data, and are used to instantiate objects."*

You should easily be able to explain not only what a class is, but how and when you would create a new one as well as what functionality it would provide in the larger context of your program.

### What is the difference between a class and a module?
The straightforward answer: *"A module cannot be subclassed or instantiated, and modules can implement mixins"*.

#### Implement mixins
Consider this use of a module:

```ruby
module Designation
  def state_designation
   "I am a member of the #{self.class}"
  end  
end

class Borg
  include Designation
end

class Federation
  include Designation
end

borg = Borg.new
=> #<Borg:0x007fe5012327e0>
kirk = Federation.new
=> #<Federation:0x007fe5012069b0>
borg.state_designation
=> "I am a member of the Borg"
kirk.state_designation
=> "I am a member of the Federation"

```
And this:

```ruby
module ExecuteOrders
  def self.go_to_warp(obj)
    "#{obj.class} ship initiates warp drive..."
  end
end

ExecuteOrders.go_to_warp(borg)
=> "Borg ship initiates warp drive..."
ExecuteOrders.go_to_warp(kirk)
=> "Federation ship initiates warp drive..."
```

Be prepared to discuss what this actually means in real life, and when you would use a module vs. a class and why.

### What is an object?
Textbook answer here is that an object is an instance of a class and has state, behavior, and identity. In a plain text example, you can say that a truck and a car are both objects of the class Vehicle, or that apple and pear are both objects of the class Fruit.

You should be able to explain in detail how object structure and behavior relate to their common class, and why this is so important in Ruby (see question 1).

### How would you declare and use a constructor in Ruby?
Constructors are declared via the initialize method and get called when you call on a new object to be created.

Using the code snippet below, calling Order.new acts as a constructor for an object of the class Order.

```ruby
class Order
  def initialize(customer, dish)
    @customer = customer
    @dish = dish
  end
end
```

### How does a symbol differ from a string?
Short answer: symbols are immutable and reusable, retaining the same object_id.

Be prepared to discuss the benefits of using symbols vs. strings, the effect on memory usage, and in which situations you would use one over the other.

### How and when would you declare a Global Variable?
Global variables are declared with the ‘$’ symbol and can be declared and used anywhere within your program. You should use them sparingly to never.

### How would you create getter and setter methods in Ruby?
Setter and getter methods in Ruby are generated with the `attr_accessor` method. attr_accessor is used to generate instance variables for data that's not stored in your database column.

```ruby
class Federation  
  attr_accessor :captain
end
```

You can also take the long route and create them manually.
```ruby
class Federation  
  def captain=(value)
    @captain = value
  end
  
  def captain
    @captain
  end
end
```
Can you give more examples build in `attr`-methods and how they can be used? 

### Describe the difference between class and instance variables?
Class variables are created with the prefix `@@` and are shared by all objects in a class.

Instance variables are created with the prefix `@` and belong to a single object within a class.

Beyond the simple textbook definition, be able to describe an example of a class and how you would use class and instance variables within it, and how they relate to issues of class inheritance.

### Explain some of the looping structures available in Ruby?
For loop, While loop, Until loop.

Be able to explain situations in which you would use one over another.