---
title: "Step 6 - More checks"
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

## Step 6 - More checks

Another check we need to do in the `withdraw` method, is to test if there are funds in the ATM, right? We can't perform a transaction if there are no funds in the machine. 

```
As a ATM operator
In order for our costumers to withdraw funds
I need make sure that we only allow withdrawals if there are funds available
``` 

The ATM has a `funds` attribute. We can perform a check if the `amount` we try to withdraw is larger then the `funds` available. 

Let's add a spec for that.

_spec/atm_spec.rb_

```ruby
[...]
it 'reject withdraw if ATM has insufficient funds' do
  # To prepare the test we want to decrease the funds value
  # to a lower value then the original 1000
  subject.funds = 50
  # Then we set the `expected_output`
  expected_output = { status: false, message: 'insufficient funds in ATM', date: Date.today }
  # And prepare our assertion/expectation
  expect(subject.withdraw(100, account)).to eq expected_output
end
```

And implement a new `when` in the `withdraw` method.

_lib/atm.rb_

```ruby
[...]
def withdraw(amount, account)
  case
  when insufficient_funds_in_account?(amount, account)
    { status: false, message: 'insufficient funds in account', date: Date.today }
  when insufficient_funds_in_atm?(amount)
{ status: false, message: 'insufficient funds in ATM', date: Date.today }
  else
[...]
```

And, we also need to create a new private method, just as we did with the previous example.

_lib/atm.rb_
```ruby
[...]
private 

def insufficient_funds_in_atm?(amount)
 @funds < amount
end

```

### The PIN code

The next check will be to make sure that the user passes in the right pin code when trying to withdraw money from his account - just as in normal life. 

```
As a Customer              
In order to keep my funds secure             
I want a secure Pin code & an expiry date on my card that allows only me access to my funds
```

We will need to modify the `withdraw` to accept a `pin_code` at one of the arguments. This will have an effect on all our tests. 

_lib/atm.rb_

```ruby
def withdraw(amount, account) -> withdraw(amount, pin_code, account)
```
So, after that change most of the tests will fail. 

Change every call to the `withdraw` method to include `1234` as the second argument. 

_spec/atm_spec.rb_

```ruby
[...]
subject.withdraw(50, '1234', account)
[...]
```

We also need to change our `instance_double` we are using for `account`. 

_spec/atm_spec.rb_

```ruby
[...]
let(:account) { instance_double('Account', pin_code: '1234') }
[...]
```

Okay, make sure that all the tests you have written up until now are passing before you move on. 

Next, introduce this test.

_spec/atm_spec.rb_

```ruby
it 'reject withdraw if pin is wrong' do
  expected_output = { status: false, message: 'wrong pin', date: Date.today }
  expect(subject.withdraw(50, 9999, account)).to eq expected_output
end
```

And implement a new `when` in the `withdraw` method.

_lib/atm.rb_

```ruby
[...]
def withdraw(amount, account)
  case
  [...]
  when incorrect_pin?(pin_code, account.pin_code)
    { status: false, message: 'wrong pin', date: Date.today }
  else
[...]
```

And again, we need to create a new private method, just as we did with the previous example.

_lib/atm.rb_

```ruby
[...]
private 

def incorrect_pin?(pin_code, actual_pin)
 pin_code != actual_pin
end

```

### Expired card

Let's tackle the check for card expiration date. 

First, let's modify our `double`.

_spec/atm_spec.rb_

```ruby
[...]
let(:account) { instance_double('Account', pin_code: '1234', exp_date: '04/17') }
[...]
```

And, as always, we write a test. (I will not include comments. By now you know what we need to do to build a test)

_spec/atm_spec.rb_

```ruby
it 'reject withdraw if card is expired' do
  allow(account).to receive(:exp_date).and_return('12/15')
  expected_output = { status: false, message: 'card expired', date: Date.today }
  expect(subject.withdraw(6, '1234', account)).to eq expected_output
end
```

And again, we need to modify the `withdraw` method. 

_lib/atm.rb_

```ruby
[...]
def withdraw(amount, account)
  case
  [...]
  when card_expired?(account.exp_date)
    { status: false, message: 'card expired', date: Date.today }
  else
[...]
```

Now, the method `card_expired?` is a little tricky. We need to make use of Ruby's `Date` object. `account.exp_date` is of String class. We need to transform it to a Date object and compare it to today's date. Examine the following implementation closely before implementing it. 

_lib/atm.rb_

```ruby
[...]
def card_expired?(exp_date)
  Date.strptime(exp_date, '%m/%y') < Date.today
end
```

Can you understand what we are doing here? 

### Disabled Account

It is time for you to start to write code on your own. There is yet another one check we need to perform. The `account_status` attribute will tell us if an account is `active` or `disabled`. 

```
As a ATM operator             
In order to allow access active customers             
I want to allow withdrawals from only active accounts
```

Our `instance_double` will be updated with this attribute to look like this. 

_spec/atm_spec.rb_

```ruby
[...]
let(:account) { instance_double('Account', pin_code: '1234', exp_date: '04/17', account_status: :active) }
[...]
```

**Things to you to consider (in random order)**

* Note that we are using a Symbol rather than a String to set `account_status`.
* You need to write a test for what happens if an account is `:disabled` 
* You need to update the output of every test that assumes that withdrawal was successful. 

**You are on your own here. If you are unsure on how to proceed make sure to go over the methods we've already created. This is a highly repetitive process at the moment. All the answers lies in in front of you. ;-) Good luck!**
 
















