# Step 2 - The core functionality

My approach to writing new software is to always do the most important thing first and get it done before I move on to other, less important, functions. What is the core functionality of this application? I would argue that creating an ATM that has some funds is the first thing that we should focus on. If there is no ATM you will not be able to do a withdrawal, right? And it the ATM has no funds you won't be able to get any cash from it either. 

So let's start with creating a `ATM` class and assign some funds to each `ATM` that we create. You already know a little bit about classes from the [Prep Course material](https://craftacademy.gitbooks.io/caa_precourse/content/ruby/classes.html).

Since we are working with TDD, we start with creating a test file first. 

As a reminder, It is important that we agree on three things at this point.

* Your **tests/specs** are placed in the `spec` folder
* Your **implementation (or production code)** are placed in the `lib` folder
* Your **settings** (like Gemfile, etc.) are placed in the main project folder

Alright?

Okay, moving on... Create a new file named `atm_spec.rb` in your `spec` folder. 

```
$ touch spec/atm_spec.rb
```
Let's add the following test to that file. Note the keywords `describe` and `it`. Also, as in all ruby programs we are creating blocks with the `do` and `end` keywords. *Make it a habit that you always add an `end` if you type `do`.*

!FILENAME spec/atm_spec.rb
```ruby
require './lib/atm.rb'
describe Atm do
  it 'has 1000$ on intitialize' do
    expect(subject.funds).to eq 1000
  end
end
```
Make sure that you run that spec from your terminal.
```
$ rspec spec/atm_spec.rb
```

What will follow now is a series of steps that aims at showing you how testing can drive your development. In the future you will probably skip some of this steps but for now, bear with me. 

If you examine the terminal output, you'll se a line like this one.

```
/your/path/atm/spec/atm_spec.rb:1:in `require': cannot load such file -- ./lib/atm.rb (LoadError)
...
```

That means that the spec file can not load `./lib/atm.rb` (where we are supposed to have our implementation code). 

Of course not, we haven't created that file yet. There's no `lib` folder yet either. Let's create all that now. 

```
$ mkdir lib
$ touch lib/atm.rb
```

Run your spec again. 

```
rspec spec/atm_spec.rb
/your/path/atm/spec/atm_spec.rb:2:in `<top (required)>': uninitialized constant Atm (NameError)
```

A new error message. But not the same as before. That is good. So what have we here?  `uninitialized constant Atm`? Yes, there is no `Atm` class defined. Let's do that. 

!FILENAME lib/atm.rb
```ruby
class Atm

end
```

Let's have another go at the spec.

```
$ rspec spec/atm_spec.rb

Atm
  has 1000$ on intitialize (FAILED - 1)

Failures:

  1) Atm has 1000$ on intitialize
     Failure/Error: expect(subject.balance).to eq 1000
     
     NoMethodError:
       undefined method `funds' for #<Atm:0x007f8043fdf2a8>
```

New error message? Cool!

Yes, there is no method `funds` for the `Atm` class. Let's add that by adding a `attr_accessor :funds` to the class. What is `attr_accessor`? You can read about it in this [Stack Overflow answer](http://stackoverflow.com/a/5046915/1354994). 

!FILENAME lib/atm.rb
```ruby
class Atm
  attr_accessor :funds
end
```

Another go at the spec and another error message. 

```
$ rspec spec/atm_spec.rb

Atm
  has 1000$ on intitialize (FAILED - 1)

Failures:

  1) Atm has 1000$ on intitialize
     Failure/Error: expect(subject.balance).to eq 1000
     
       expected: 1000
            got: nil
```

Okay, so we expected `funds` to be `1000` but it was `nil`. Let's make it so that every time an Atm object is instantiated whe balance is automatically set to 1000. 

We can do that by setting that value in the `initialize` method. `initialize` is a constructor method that will be run every time an instance of a class is created. 

!FILENAME lib/atm.rb
```ruby
class Atm
  attr_accessor :funds
  
  def initialize
    @funds = 1000
  end
end
```

And now, when you run RSpec, the test passes. 

```
$ rspec spec/atm_spec.rb

Atm
  has 1000$ on intitialize

Finished in 0.00195 seconds (files took 0.67858 seconds to load)
1 example, 0 failures
```

Yay! First success! Green is GOOD!

**Lesson learned:**  Every feature, no matter how small, will lead to a series of failures. Until it doesn't. This goes for new, inexperienced programmers, as well as for those of us who has been doing this for a long time. There's nothing wrong with you. Just get used to it and see everything you do as a learning experience. 

Keep your calm, read the error messages that RSpec so kindly throws at you and make small steps forward. Be thankful that you have a testing framework that helps you to figure out what is wrong with your code. Imagine if you ware coding without it? 

Alright, enough of coding philosophy. Let's move on. This is a great time to do a commit and push up your code (Unless you already did that). 


###Doing a withdrawal

Let's add another test to the `atm_spec`. Inside the `describe Atm`block, add this spec.

!FILENAME spec/atm_spec.rb
```ruby
it 'funds are reduced at withdraw' do
  subject.withdraw 50
  expect(subject.funds).to eq 950
end
```

In my spec file that `it` block starts at line 7. What I can do is to run JUST that particular block, instead of the entire spec file. It might seem trivial right now, but further down the road we'll have dozens of specs and trust me, you don't want to keep running them all at once. 

```
$ rspec spec/atm_spec.rb:7
Run options: include {:locations=>{"./spec/atm_spec.rb"=>[7]}}

Atm
  funds are reduced at withdraw (FAILED - 1)

Failures:

  1) Atm funds are reduced at withdraw
     Failure/Error: subject.withdraw 50
     
     NoMethodError:
       undefined method `withdraw' for #<Atm:0x007fac30e79378 @balance=1000>
...
```
Shoots! New error. Yes, yes, yes. That is supposed to happen! ;-)

So, we have an `undefined method 'withdraw'`. Alright. let's create the `withdraw` method and let it take one argument - the amount we want to withdraw from the Atm. 

!FILENAME lib/atm.rb
```ruby
class Atm
  attr_accessor :balance
  
  def initialize
    @balance = 1000
  end
  
  def withdraw(amount) 
  end
end
```

Run RSpec just to see another error message. 

```
$ rspec spec/atm_spec.rb:7
Run options: include {:locations=>{"./spec/atm_spec.rb"=>[7]}}

Atm
  funds are reduced at withdraw (FAILED - 1)

Failures:

  1) Atm funds are reduced at withdraw
     Failure/Error: expect(subject.balance).to eq 950
     
       expected: 950
            got: 1000
...
```

Okay, I think you get the point now. We don't have to follow the error messages in such detail from now on.

Let's add some functionality to the `withdraw` method that actually adjust the `balance`. 

!FILENAME lib/atm.rb
```ruby
class Atm
  #...
  
  def withdraw(amount) 
    @funds -= amount
  end
end
```

And when you run RSpec again, the test passes. Another one bites the dust!



