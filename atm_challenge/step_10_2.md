## Step 10 - Integrating all parts

Alright, at this stage we can create an `Atm`, we can create a `Person` that has an `Account`. The `Person`can have `cash` in pocket or hold his money in his `Account`. All pretty straight forward.

Now we want to create a method that allows a person to withdraw funds from a specific atm and when he does that 3 things should happen:

1. The balance of the account should DECREASE
2. The funds in the ATM should DECREASE
3. The cash in pocket should INCREASE

Consider these specs.

!FILENAME spec/person_spec.rb
```ruby
  describe 'can manage funds if an account been created' do
    [...]

    it 'funds are added to the accounst balance - deducted from cash' do
      subject.cash = 100
      subject.deposit(100)
      expect(subject.account.balance).to be 100
      expect(subject.cash).to be 0
    end

    it 'can withdraw funds' do
      command = lambda { subject.withdraw(amount: 100, pin: subject.account.pin_code, account: subject.account, atm: atm) }
      expect(command.call).to be_truthy
    end

    it 'funds are added to cash - deducted from accounst balance' do
      subject.cash = 100
      subject.deposit(100)
      subject.withdraw(amount: 100, pin: subject.account.pin_code, account: subject.account, atm: atm)
      expect(subject.account.balance).to be 0
      expect(subject.cash).to be 100
    end
  end

``` 

Note: There are some new commands and techniques in the code above. Google them, talk to your peers and figure out WHY we are using them so you get at good understanding of what we are doing. I will be going over some of them in my talks and break-out sessions but it is up to you to find the best 
