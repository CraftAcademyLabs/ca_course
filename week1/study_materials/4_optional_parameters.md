### Difference between optional and standard parameters.

First of all we must know the difference between parameters and arguments:

A parameter is a variable in the method definition. When a method is called, the arguments is the data you pass into the method's parameters.

To clarify:
Parameter is variable in the declaration of function. The argument is the actual value of this variable that gets passed to function.

Now when the difference between parameters and arguments are clear, lets consider the Car class below.

```ruby

    class Car

    attr_accessor :brand, :color

        def initialize(brand, color)
            @brand = brand
            @color = color
        end

    end

```

Creating a new Car object means that we need to pass in the arguments `'Toyota'` and `'metallic'` so that a new instance of the car class will be created. like this:

```bash
2.4.0 :001 > new_car = Car.new('Toyota', 'metallic')
=> #<Car:0x007faf628941f8 @brand="Toyota", @color="metallic">
```

Otherwise we will get the error:

```bash
    ArgumentError: wrong number of arguments (given 0, expected 2)
```

Go ahead and try it out in irb.

---

So lets rewrite our car class a little

```ruby

    class Car

        attr_accessor :brand, :color

        # We add the options = {} instead of color, and brand
        def initialize(options = {})

        # We replace the color and brand with options[:brand] and options[:color]
            @brand = options[:brand]
            @color = options[:color]
        end
    end

```

Now let's create a new instance of the `Car` class

```bash
2.4.0 :001 > new_car = Car.new
=> #<Car:0x007faf628941f8 @brand=nil, @color=nil>
```

When we create a new Car object without passing in the arguments we do not get an error, however if we examine the object that is created a little bit closer we can see that the `@brand` and `@color` is `nil`. This means that we do not get the `ArgumentError` and the object is created. But we still have to pass in the arguments in order to set the values.


So what would we do if we want to pass in arguments to the Car class? Well beacuse we added the `options = {}` we now need to pass in the arguments in the form of a `key:value` pair

```bash
2.4.0 :001 > new_car = Car.new(brand: 'Toytota', color: 'metallic')
=> #<Car:0x007faf628941f8 @brand='Toytota', @color='metallic'>
```

We could also pass in just one argument, try this in `irb` and examine the output.

The benefits of using optional parameters are that you do not get an error and that you know which arguments you are passing in.
