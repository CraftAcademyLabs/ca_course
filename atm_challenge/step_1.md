# Step 1

WIP: 

```ruby
class Account
  attr_reader :balance, :holder

  def initialize(name, balance=100)
    self.holder = Person.new(name: name)
    self.balance = balance
  end
  
  def display_balance(pin_number)
    if pin_number == pin
      puts "Balance: #{@balance}"
    else
      puts pin_error
    end
  end

  def withdraw(pin_number, amount)
    if pin_number == @pin
      @blanace -= amount
      puts "Withdrew #{amount}. New balance: #{balance}"
    else
      puts pin_error
    end
  end

  private

  def pin
    self.pin = 1234
  end

  def pin_error
    return "Access denied: incorrect PIN."
  end
end
```

