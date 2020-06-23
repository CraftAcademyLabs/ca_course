## Advanced Ruby
### What is a Block?
Blocks are anonymous functions that can be passed into methods. Blocks are enclosed in a `do end` statement or between brackets `{}`, and they can have multiple arguments.

##### single line blocks
```ruby 
[1, 2, 3].each { |num| puts num }
```
##### multi-line blocks
```ruby

[1, 2, 3].each do |num|
  puts num
end
```

### What is a Proc?
A `Proc`, short for procedure, acts similar to a block, but can be saved as variables and reused. Think of them as blocks you can call over and over again on multiple arrays and as anonymous methods (or nameless functions) containing code.

Be prepared to explain an instance when you would use a proc over a block.

### What is a lambda?
Lambdas are very similar to procs in terms of functionality. However, they have a few key differences. Lambdas check the number of arguments passed and will return an error if you try to pass the wrong number (a `Proc` sets extra variables to nil).

The other difference is that lambdas can handle a return function, whereas procs will return an error.

### What are the three levels of method access control for classes and what do they signify? What do they imply about the method?

The short answer is: Public, protected, and private.

All methods can be accessed within the class. But what about outside callers?

**Public methods** can be called by all objects and subclasses of the class in which they are defined in.

**Protected methods** are only accessible to objects within the same class.

**Private methods** are only accessible within the same instance.

Be able to explain why this does or doesn’t matter, and when you would want to set a method as private.

### There are three ways to invoke a method in ruby, can you name them?
The answer is: the **dot operator** (or **period operator**), the **Object#send** method, or **method(:foo).call**

```ruby
class Foo
   def bar
     'bar has been called'
   end   
end  
=> :bar
obj = Foo.new
=> #<Foo:0x007f8a83139fb0>
obj.bar
=> "bar has been called"
obj.send(:bar)
=> "bar has been called"
obj.method(:bar).call
=> "bar has been called"
```
### What does `self` mean?

`self` always refers to the current object. This question is more difficult than it seems because classes are also objects in ruby.

```ruby
class Foo
  def show_me_self
    "At the instance level, self is #{self}"
  end  

  def self.show_me_self
   "At the class level, self is #{self}"
  end  
end  

Foo.show_me_self
=> "At the class level, self is Foo"
Foo.new.show_me_self
=> "At the instance level, self is #<Foo:0x007f8a8401e490>"
```
The example code indicates two things:

- at the class level, self is the class, in this case Foo.
- at the instance level, self is the instance in context, in this case the instance of Foo at memory location 0x007f8a8401e490.

### What does it mean that Ruby has open classes?
It means that at run time the definition of a class can be changed. All classes in Ruby are open to be changed by the user at all times.

In Ruby, classes are never closed: you can always add methods to an existing class. This applies to the classes you write as well as the standard, built-in classes. All you have to do is open up a class definition for an existing class, and the new contents you specify will be added to whatever's there. (Monkeypatching)

Consider this:
```ruby
class Fixnum
   def +(num)
     self * num #or whatever you want to do
   end  
end
3 + 3
=> 9
```

Weird, right?
