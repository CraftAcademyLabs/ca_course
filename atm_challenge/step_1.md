# Step 1

WIP: 

```ruby
class Person
    attr_accessor :name
    def initialize(options = {})
        self.name = options[:name]
    end
end
```

```ruby
class Account
  attr_accessor :balance, :holder, :pin
  MINIMUM_DEPOSIT = 100

  def initialize(name, options = {})
    self.holder = Person.new(name: name)
    self.balance = options[:balance] || MINIMUM_DEPOSIT
    self.pin = 1234
  end
  
  def current_balance(pin_number)
    if pin_number == pin
      {balance: self.balance}
    else
      {message: pin_error}
    end
  end

  def withdraw(pin_number, amount)
    if pin_number == self.pin
      self.balance -= amount
      {withrawal: amount, balance: self.balance}
    else
      {message: pin_error}
    end
  end

  private

  def pin_error
    "Access denied: incorrect PIN."
  end
end
```

