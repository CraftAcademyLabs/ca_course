## Step 7 - Cash is King

Yeah, remember when we kind of skipped adding bills to our successful output? We can't ignore that requirement any linger. After all, the cash is the reason we use Atm's.

A prat of the output our program i supposed to return on successful withdrawal is an array of bills. We know that the Atm is supposed to hold 5, 10 & 20$ bills, right? 

We also know that one of the conditions for a successful transaction is that the amount a user can withdraw must be divisible by 5. 

Knowing this we can build a method that tells us what bills we will get from the Atm. 

Let's try some stuff out in irb. **As always, read the comments as carefully as anything else in this documentation.**

```ruby 
18:05 $ irb
# create an array of denominations. We need to start with the biggest value. 
# wnat to know why? Try the other way around for yourself.
2.2.1 :001 > denominations = [20, 10, 5]
 => [20, 10, 5]
 
# create an embty array and store it in a variable called `bills`
# this object will be populated with our bills.
2.2.1 :002 > bills = []
 => [] 
 
# choose an arbitrary amount - remember that we need to stick to the 
# business objective. The amount needs to be divisible by 5.
2.2.1 :003 > amount = 65
 => 65 

# here comes the tricky part with the `while` loop. 
# for each value in the `denominations` array, we subtract it
# from `amount` until amount is lower than zero.
# at the same time we `push` the value into the `bills` array.
2.2.1 :004 > denominations.each do |bill|
2.2.1 :005 >         while amount - bill >= 0
2.2.1 :006?>           amount -= bill
2.2.1 :007?>           bills << bill
2.2.1 :008?>         end
2.2.1 :009?>       bills
2.2.1 :010?>   end
 => [20, 10, 5] 
 
# and now, if we check the `bills` array, we can see that there are 4 positions. 
# 3 x 20$ ans 1 x 5$. Cash is king
2.2.1 :011 > bills
 => [20, 20, 20, 5] 
2.2.1 :012 > 
```

The same code without my comments and the irb linenumbers.

```ruby
denominations = [20, 10, 5]
bills = []
amount = 65	
denominations.each do |bill|
    while amount - bill >= 0
      amount -= bill
      bills << bill
    end
    bills
end
bills
```

A few words about this type of loops in general and specifically the `while` loop.
A loop is a flow control method that will make code run a number of times until some condition is met.   
####The while loop
`while` allows you to specify the condition that must be `true` to keep looping, then the condition is evaluated to `false` the loop exits. An example

    count = 0
    while count < 5
      print 'The count is now ', count, '. '
      count = count + 1
    end
    Result: The count is now 0. The count is now 1. The count is now 2. The count is now 3. The count is now 4.
####The until loop
The until loop is a variation on the while loop but reverse. ;-)

    count = 0
    until count == 5
      print 'The count is now ', count, '. '
      count = count + 1
    end
    
    Result: same as above.

** *Pay attention to the double equal signs. In Ruby `a == 5` means "Compare a to 5", and `a = 5` means "Set the value of a to 5".* **

Back to our implementation. 

As usual, let's start with our test. We don't necessarily need to add any new specs but rather modify the ones we already have written. 

Let's revisit this spec.

!FILENAME  spec/atm_spec.rb
```ruby
it 'allow withdraw if account has enough balance.' do
  expected_output = { 
      status: true, 
      message: 'success', 
      date: Date.today, 
      amount: 45,
      bills: [20, 20, 5]}
  expect(subject.withdraw(45, '1234', account)).to eq expected_output
end
```

Apart from a slightly updated formatting, we've added `bills: [20, 20, 5]` to the `expected_output`. 

If you run this spec now, it will go red. 

Let's add `bills:` to the output and create a method that builds upon our experimental code above and populates the array. 

!FILENAME  lib/atm.rb
```ruby
[...]
def perform_transaction(amount, account)
  @funds -= amount
  account.balance = account.balance - amount
  { status: true, message: 'success', date: Date.today, amount: amount, bills: add_bills(amount) }
end

def add_bills(amount)
  denominations = [20, 10, 5]
  bills = []
  denominations.each do |bill|
    while amount - bill >= 0
      amount -= bill
      bills << bill
    end
  end
  bills
end
[...]
```

With this we are closing to the end of the line with the Atm class. 





