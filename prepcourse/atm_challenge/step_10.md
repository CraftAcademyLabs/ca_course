## Step 10 - Integrating all parts

Alright, at this stage we can create an `Atm`, we can create a `Person` that has an `Account`. The `Person` can have `cash` in pocket, or hold his money in his `Account`. All pretty straight forward.

Now, we want to create a method that allows a person to withdraw funds using a specific atm machine, and when he does that 3 things should happen:

1. The **balance of the account** should DECREASE
2. The **funds in the ATM** should DECREASE
3. The **cash in pocket** should INCREASE

Consider these specs.

_spec/person_spec.rb_

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

  it 'withdraw is expected to raise error if no ATM is passed in' do
    command = lambda { subject.withdraw(amount: 100, pin: subject.account.pin_code, account: subject.account) }
    expect { command.call }.to raise_error 'An ATM is required'
  end

  it 'funds are added to cash - deducted from account balance' do
    subject.cash = 100
    subject.deposit(100)
    subject.withdraw(amount: 100, pin: subject.account.pin_code, account: subject.account, atm: atm)
    expect(subject.account.balance).to be 0
    expect(subject.cash).to be 100
  end
end

``` 

Note: There are some new commands and techniques in the code above. Google them, talk to your peers and figure out WHY we are using them so you get at good understanding of what we are doing. I will be going over some of them in my talks and break-out sessions, but it is up to you to find the best way of using them.

Now I will show you how I implemented the `Person` class - at least in the first development cycle (there are plenty of room for refactoring, and I expect you to improve on this code). 

_lib/person.rb_ 

```ruby
require './lib/account'

class Person
  attr_accessor :name, :cash, :account

  def initialize(attrs = {})
    @name = set_name(attrs[:name])
    @cash = 0
    @account = nil
  end

  def create_account
    @account = Account.new(owner: self)
  end

  def deposit(amount)
    @account == nil ? missing_account : deposit_funds(amount)
  end

  def withdraw(args = {})
    @account == nil ? missing_account : withdraw_funds(args)
  end


  private

  def deposit_funds(amount)
    @cash -= amount
    @account.balance += amount
  end

  def withdraw_funds(args)
    args[:atm] == nil ? missing_atm : atm = args[:atm]
    account = @account
    amount = args[:amount]
    pin = args[:pin]
    response = atm.withdraw(amount, pin, account)
    response[:status] == true ? increase_cash(response) : response
  end

  def increase_cash(response)
    @cash += response[:amount]
  end

  def set_name(name)
    name == nil ? missing_name : name
  end

  def missing_name
    raise ArgumentError, 'A name is required'
  end

  def missing_account
    raise RuntimeError, 'No account present'
  end

  def missing_atm
    raise RuntimeError, 'An ATM is required'
  end
end
```
