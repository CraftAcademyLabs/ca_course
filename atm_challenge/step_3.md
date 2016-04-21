# Step 3

Okay, so we have a basic `withdraw` method for our Atm class. It's a good start. Now, if we have a look at the requirements we initially got from our client, we see that a successful withdraw should generate a response in the form of a Hash. 

**This hash is the equivalent of a receipt that the Atm prints out in the real life.** It should look like this if the transaction was successful:

```
{ status: true, message: 'success', date: '2016-01-30', amount: 35, bills: [20,10,5]}
```

For unsuccessful transactions, it should look like this:

```
{ status: false, message: '[reason for failure e. e. wrong pin]', date: '2016-01-30'}
```

Let's break this down. 

**`status`** 
- Can be `true` or `false` depending if the transaction was successful.

**`message`** 
- A message to the user. We can set that to `success` when the transaction was successful and to something else if we for some reason can not perform the transaction. 

**`date`**
- The date of the transaction - simply today's date.

**`amount`** 
- Visible only when transaction was successful. 
- Simply the amount that wes withdrawn.

**`bills`**
- Visible only when transaction was successful.
- An array of bills that was dispached by the Atm. This symbolize the actual cash you would get in real life. 

###Testing the happy path
The first test we will write is the so called "Happy Path". We know that a transaction can either bu successful or rejected for some reason. We'll get back to the rejections (that is the lionshare of the work that lies ahead of us). At this stage, let's focus on a simple successfull transaction. 

Let's start with preparing our test.

The Atm needs to interact with another class - we will call it `Account`. The Account class will symbolize both the bank account and a card we can use in the Atm (there is no need to create both an Account class and a Card class for the sake of this prototype).

However, we have not created that class yes, so in out `atm_spec` we will use a so called `double` in order to be able to test the functionality. Doubles are objects that can be used as stand-ins for other objects (hence the name `double`). We will go over doubles more extensivly further down the road in the camp.

For now, let's define a `douvle` in our spec and give it a name od `account`

!FILENAME spec/atm_spec.rb
```ruby
describe Atm do
  let(:account) {double(:account)}
  [...]
end
```

Okay, we want the `withdraw` method to have access to the `account` object in order to know things about it. Things like a `balance` for instance, right? **The Atm needs to know if there is enough funds i the account before it clears the transaction.**

First we will write a test and then we will modify the implementation code.

**Note:** *Make sure that you read the comments in the `it` block below but **do not include them** in your spec.*

!FILENAME spec/atm_spec.rb
```ruby
describe Atm do
  [...]
  it 'allow withdraw if account has enough balance.' do
  
    # We need to add an attribute of `balance` to the `account` object
    # and set the value to `100`
    allow(account).to receive(:balance).and_return(100)
    
    # We need to tell the spec what to look for as the responce
    # and store that in a variable called `expected_outcome`.
    # Please note that we are omitting the `bills` part at the moment,
    # We will modify this test and add that later.

    expected_output = { status: true, message: 'success', date: Date.today, amount: 45}
    
    # We need to pass in two arguments to the `withdraw` method. 
    # The amount of money we want to withdraw AND the `account` object.
    # The reason we pass in the `account` object is that the Atm needs
    # to be able to access information about the `accounts` balance
    # in order to be able to clear the transaction. 
    
    expect(subject.withdraw(45, account)).to eq expected_output
  end
end
```

Now, make sure you run this spec and study the error messages from Rspec carefully. 

In order to make this pass, we need to modify the `withdraw` method in our implementation code. Again, be mindful of the comments in the code below.

!FILENAME spec/atm_spec.rb
```ruby
def withdraw(amount, account)
  # We will be using Ruby's `case`- `when` - `then` flow control statement
  # and check if there is enough funds in the account
  case
  when amount > account.balance then
    # we exit the method if the amount we want to withdraw is bigger than 
    # the balance on the account
    break
  else
    # If it's not, we perform the transaction
    # We DEDUCT the amount from the Atm's funds
    @funds = @funds - amount
    # We also DEDUCT the amount from the accounts balance
    account.balance = account.balance - amount
    # and we return a responce for a successfull withdraw.
    { status: true, message: 'success', date: '2016-01-30', amount: amount}
  end
end
```









