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

###Class methods
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


###Module methods
```ruby
module FooModule

  def self.foo(bar)
    bar
  end
end
```

```ruby
module FooModule
  extend self

  def foo(bar)
    bar
  end
end
```

```ruby
module FooModule

  module_function
  def foo(bar)
    bar
  end
end
```





