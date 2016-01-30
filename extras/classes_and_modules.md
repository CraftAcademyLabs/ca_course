## Classes and Modules

Classes are about objects; Modules are about functions.

Modules are about providing methods that you can use across multiple classes - think about them as "libraries". A Module is just a collection of methods and constants and comes in handy at times when you want to group things together that don't naturally form a Class. Classes are also a collection of methods and constants, but with the added functionality of being able to be instantiated. A Module cannot be instantiated. 

|               | Class                                                        | Module                                                                                                                                      |
|---------------|--------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Instantiation | can be instantiated                                          | can *not* be instantiated                                                                                                                   |
| Usage         | object creation                                              | mixin facility, provide a namespace.                                                                                                        |
| Superclass    | Module                                                       | Class                                                                                                                                       |
| Methods       | class methods and instance methods                           | module methods and instance methods                                                                                                         |
| Inheritance   | can inherit behaviour and can be base for inheritance        | no inheritance                                                                                                                              |
| Inclusion     | cannot be included                                           | can be included in classes and modules by using the `include` command (includes all instance methods as instance methods in a class/module) |
| Extension     | can not extend with `extend` command (only with inheritance) | module can extend instance by using `extend` command (extends given instance with singleton methods from module)                            |

###Class - methods
Class methods are methods that are called on a class and instance methods are methods that are called on an instance of a class.

```ruby
class FooClass
  def self.bar
    'class method'
  end
  
  def baz
    'instance method'
  end
end

FooClass.bar # => "class method"
FooClass.baz # => NoMethodError: undefined method ‘baz’ for Foo:Class

FooClass.new.baz # => instance method
FooClass.new.bar # => NoMethodError: undefined method ‘bar’ for #<Foo:0x1e820>

```


###Module - methods

The methods in a module may be instance methods or module methods. You can include[^1] methods in your module that you can be both functions or included by one or several Classes and used as instance methods. Instance methods appear as methods in a class when the module is included, module methods do not. Conversely, module methods may be called without creating an encapsulating object, while instance methods may not.



There are several ways you can create methods in a Module. Let's say you want to be able to do
```ruby
FooModule.foo(some_value)
```
You can define the `foo` method like this
```ruby
module FooModule

  def self.foo(bar)
    bar
  end
end
```
Or like this...
```ruby
module FooModule
  extend self

  def foo(bar)
    bar
  end
end
```
...or like this.
```ruby
module FooModule

  module_function
  def foo(bar)
    bar
  end
end
```

[^1]: Foxes are red




