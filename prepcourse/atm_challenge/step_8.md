---
title: "Step 8 - The Account"
subtitle: "Ruby basics - ATM Challenge"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Rspec, Ruby"
keywords: [Rspec, Ruby]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

## Step 8 - The Account

Now that we are finished (at least for now) with the Atm class, we should move forward and create the `Account` class.

```
As a Customer
In order to manage my funds
I need to have a personal account
```


We will go over the same steps as we did when creating the `Atm` class. 

1. create a test file (`account_spec.rb`) and
2. create a implementation file (`account.rb`)

In the spec file we'll be writing our spec for code that we are planning to implement. As usual there are decisions about what attributes to add to the class and what methods needs to be defined.

Fortunately, we've already have much of the necessary  
information about that class to make those decisions easier. 

Remember the `class_double` we used while testing the `Atm`'s functionality? If we examine that object closely we can see that the attributes we used ware `:pin_code`, `:balance`, `:account_status` and `:exp_date`. At the very least these are the attributes we need to assign to the `Account` class definition. 

There is one thing that the `Account` should have, that was never needed in the interaction wit `Atm` but that should be a part of it. That is an account owner. 

We'll let you code on your own but provide an recording that showcase a possible workflow. 

Some tips for testing.

A **Pin number** is generally a 4 digit number. One way to check if a number has 4 digits is this.

```ruby
it 'check length of a number' do
  number = 1234
  number_length = Math.log10(number).to_i + 1
  expect(number_length).to eq 4
end
```

Tips: Try running parts of this in IRB if you wonder why the `Math.log10` method can be used for this and why we need to call `.to_i` and `+ 1` on the result. 

Also, I would suggest that we randomize the pin code when we initialize a new Account object. A 4 digit number can be randomly generated with:

```ruby
rand(1000..9999)
```


### Programming showcase -  Pin and Balance

<iframe width="420" height="315" src="https://www.youtube.com/embed/WTS_o121xIo" frameborder="0" allowfullscreen></iframe>

## Expiry date

The **expiry date** on atm cards (and other credit cards) is generally stored in the format of month/year - like "04/16" that translates to April 2016.

When we set the :exp_date we need to make the calculation of today's date and add a predefined amount of years that we want the card to be valid for (remember that for the purpose of this exercise, the account symbolizes BOTH a bank account AND an atm card)  

Let's write a test to see if the `:exp_date` is set in `initialize`. 

<small>spec/account_spec.rb</small>
```ruby
it 'is expected to have an expiry date on initialize' do
    # Here we set the validity of the card to 5 yrs as default
    expected_date = Date.today.next_year(5).strftime("%m/%y")
    expect(subject.exp_date).to eq expected_date
end
```

So, how can we implement a method to set an expiry date when an account object is created?

First of all, we need to tell the class that the default validity of the card is 5 yrs. To do that, we can set a type of variable called constant with the default value of how many years a new card should be valid for. It can look something like this.

```ruby 
class Account 
  STANDARD_VALIDITY_YRS = 5
end
```
(I would like you to find out why I use capital letters in the variable name and find out what we mean when we say that you need to avoid Magic Numbers in your code.) 

When we set the `:exp_date` value, we can start with today's date and add the value of the constant and finish off by formatting the date the way we want it. It could look something like this.

```ruby 
def set_expire_date
  Date.today.next_year(Account::STANDARD_VALIDITY_YRS).strftime('%m/%Y')
end
```
(Do you really need `Account::STANDARD_VALIDITY_YRS`? Perhaps `STANDARD_VALIDITY_YRS` in enough? Try it out...)

## Account status

Another check that the Atm does on the Account is to check the `:account_status`. That will be the next thing we implement on the class. 

We start with a spec.

<small>spec/account_spec.rb</small>
```ruby
it 'is expected to have :active status on initialize' do
  expect(subject.account_status).to eq :active
end
```

Notice that we are using the datatype Symbol to set the `:account_status`. 

And again we need to set that attribute in our `initialize` method.

<small>lib/account.rb</small>
```ruby
  def initialize
    [...]
    @account_status = :active
  end
```

Okay, so an account has status of `:active` when it is instantiated (created) . But how about if we would like to deactivate an account. We could simply set the value of the `:account_status` attribute to `:deactivated`. But we can (and should) create a method for that. The question is if we should create a **Class method** or an **Instance method**. You need to research the difference between this type of methods but consider this two different approaches.

```ruby
def self.deactivate(account)
  account.account_status = :deactivated
end 

def deactivate
  @account_status = :deactivated
end 
```

Examine the two following ways of using the methods above.

```ruby
it 'deactivates account using Class method' do
  Account.deactivate(subject)
  expect(subject.account_status).to eq :deactivated
end

it 'deactivates account using Instance method' do
  subject.deactivate
  expect(subject.account_status).to eq :deactivated
end
```

**Use the one that you find best in your implementation but please be ready to make an argument about your choice.** 

## The Account Owner

Each account that we create should have an owner. The owner should have a class of his own. For the moment that class is not defined so we will go ahead and pretend again that there is an instance of Person by using an `instance_double`.


<small>spec/account_spec.rb</small>
```ruby
# we create the double in our `describe` block and give him one sigle attribute
let(:person) {instance_double('Person', name: 'Thomas')}
# and modify our `subject` 
subject { described_class.new({owner: person}) }
```

First let's see if the owner is being set.

```ruby
it 'is expected to have an owner' do
  expect(subject.owner).to eq person
end
```
And add a test for a mandatory owner for each instance of Account.

<small>spec/account_spec.rb</small>
```ruby
it 'is expected to raise error if no owner is set' do
  expect { described_class.new }.to raise_error 'An Account owner is required'
end
```

Note that we are using a new matcher (`raise_error`). There are many more matchers we can use when writing our tests. Take some time to read through the documentation about [RSpec's built-in matchers](https://www.relishapp.com/rspec/rspec-expectations/v/3-4/docs/built-in-matchers/). 

Moving on to the implementation to make these two specs to pass (as usual, this is a suggestion, examine the code closely before using it).

<small>lib/account.rb</small>
```ruby
[...]
def initialize(attrs = {})
  [...]
  set_owner(attrs[:owner])
end

private
  def set_owner(obj)
  # here we are using a Ternary Operator for the first time
  # take a look at this StackOverflow aswer to find out more
  # http://stackoverflow.com/a/4252945
  obj == nil ?  missing_owner : @owner = obj
end

def missing_owner
  raise "An Account owner is required"
end
```


















