## Step 6 - Testing the sad path

Okay, so now we can do a withdrawal IF there is money in the account.  

Let's start thinking about everything that can go wrong with a withdrawal. 

For starters, what if there is not enough money in the account? At this point we only `return` from the method without any feedback for the user.

Let's change that.


!FILENAME spec/atm_spec.rb
```ruby
[...]
it 'reject withdraw if account has insufficient funds' do
  expected_output = { status: true, message: 'insufficient funds', date: Date.today }
  # We know that the account created for the purpose of this test
  # has a balance of 100. So let's try to withdraw
  # a larger amount. In this case 105.
  expect(subject.withdraw(105, account)).to eq expected_output
end
[...]
```

This test should fail for you. 

The following implementation is needed.

!FILENAME lib/atm.rb
```ruby
def withdraw(amount, account)
  case
  when insufficient_funds_in_account?(amount, account) then
    { status: true, message: 'insufficient funds', date: Date.today }
  else
    perform_transaction(amount, account)
  end
end
```





