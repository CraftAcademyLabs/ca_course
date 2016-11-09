# Step 4 - Refactoring

Okay, we will stop here for a moment ad do some changes to our code to make it more readable and to follow the principle that each method should only have one responsibility. In our case, the way we have written the `withdraw` method, the method perform several tasks. Since we will be developing that method further, we want to introduce a better, more readable structure.

In practical terms, we want to extract some of this methods responsibilities to separate, so called `private` methods. 

1. We want to extract the check of `account.balance` to a separate method.
2. We want to extract the transaction to a separate method.

Evaluate this code carefully.

!FILENAME lib/atm.rb
```ruby 
class Atm
  attr_accessor :funds

  def initialize
    @funds = 1000
  end

  def withdraw(amount, account)
    # We will be using Ruby's `case`- `when` - `then` flow control statement
    # and check if there is enough funds in the account
    case
    when insufficient_funds_in_account?(amount, account) then
      # we exit the method if the amount we want to withdraw is bigger than
      # the balance on the account
      return
    else
      # If it's not, we perform the transaction
      perform_transaction(amount, account)
    end
  end

  private

  def insufficient_funds_in_account?(amount, account)
    amount > account.funds
  end

  def perform_transaction(amount, account)
    # We DEDUCT the amount from the Atm's funds
    @funds -= amount
    # We also DEDUCT the amount from the accounts balance
    account.balance = account.balance - amount
    # and we return a responce for a successfull withdraw.
    { status: true, message: 'success', date: Date.today, amount: amount }
  end
end

```

Note that we have NOT made any changes to our test and if you run them now, they should all pass green. 

**Refactoring is all about that. You make your code better WITHOUT introducing any new functionality. **

