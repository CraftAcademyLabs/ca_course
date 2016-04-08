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
    expect(subject.balance).to eq 1000
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
