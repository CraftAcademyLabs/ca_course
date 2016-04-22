## Step 8 - The Account

Now that we are finished (at least for now) with the Atm class, we should move forward and create the `Account` class. 

We will go over the same steps as we did when creating the `Atm` class. 

1. create a test file (`account_spec.rb`) and
2. create a implementation file (`account.rb`)

In the spec file we'll be writing our spec for code that we are planning to implement. As usual there are decisions about what attributes to add to the class and what methods needs to be defined.

Fortunately, we've already have much of the necessary  
information about that class to make those decisions easier. 

Remember the `class_double` we used while testing the `Atm`'s functionality? If we examine that object closely we can see that the attributes we used ware `:pin_code`, `:balance` and `:exp_date`. At the very least these are the attributes we need to assign to the `Account` class definition. 

There is one thing that the `Account` should have, that was never needed in the interaction wit `Atm` but that should be a part of it. That is an account owner. 

we'll let you code on your own but provide an recording that showcase a possible workflow. 

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

The expiry date on atm cards (and other credit cards) is generally stored in the format of month/year - like "04/16" that translates to April 2016.

When we set the :exp_date we need to make the calculation of todys date and add a predefined amount of years that we want the card to be valid for (remember that for the purpose of this excercise, the account symbolizes BOTH a bank account AND an atm card)  

To do that, we can set a type of variable called constant with the default 


