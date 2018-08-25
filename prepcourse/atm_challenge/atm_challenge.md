---
title: "Ruby basics - ATM Challenge"
author: [Craft Academy]
date: Version 0.1 - 2018
subject: "Ruby"
keywords: [Ruby, Example, Learn To Code]
subtitle: "Your First Ruby Challenge"
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Ruby basics - ATM Challenge

Welcome to your first Ruby assignment. We will start the next step on your journey toward becoming a programmer/developer/coder by bulding a small piece of software that is suppposed to simulate an ATM (or as we call it in swedish: Bankomat)

![](http://alkredit.az/resources/content_img/logos/bankomat.png)

Withdrawing money from an ATM is fairly common, everyday action, that we all have done at some point, right? You are in need of some cash (for whatever reason) and you have your Visa or MasterCard with you, you spot an ATM machine, you walk up to it, insert your card, enter your pin and you grab a few bucks or what have you. But what is exactly happening in this situation, or rather this perticular interaction between you and that piece of machine? 

The charackteristics of this interaction makes it a good first step into understanding how software works and how we can understand requirements and turn them into features. 

This is now walk in the part. Just becouse the problem set in itself does not seem overly complicated, you will need to stay alert, employ everything you've learned so far and work hard if you want to compleate this challege. 

Work hard, pay attention to details and collaborate with your fellow students, and you'll be all good. 


### Learning objectives
- Review fundamentals learned in the prep course
- Learn about Ruby classes, modules, methods and attributes
- Learn about unit testing with RSpec and the benefits of writing automated tests
- Learn about naming standards
- Learn about using double, class_double and instance_double
- Learn about debugging and common programming techniques

### The challenge
Our client is a financial institution that wants to allow its customers to withdraw funds from their accounts using an Automatic Teller Machine (ATM). They have turned to us for a prototype of a system with limited functionality. Our job is to write a simple Ruby program that can be run in the Interactive Ruby Shell (IRB).

**You will be working with your Coach and your peers to get started with using RSpec as a testing framework an with implementing your code.**



### Scope
The following objectives must be met:

- An ATM machine can hold up to $1000
- Withdrawal can be cleared only if 
    - The ATM holds enough funds
    - The amount is divisible by 5
    - the person attempting the withdrawal provides a valid ATM card
        - Valid pin and expire date
        - Card status must be active (Not report stolen or lost)
    - the person attempting the withdrawal has sufficient funds in his account
- There are only $5, $10 and $20 bills in the ATM. Withdrawals for amounts not divisible by 5 must be rejected.
- Upon a successful withdrawal the system should return a receipt with information about the date, amount and bills that was dispatched. (The receipt should be presented in the form of a Hash

#### Example output
```ruby
# Message on successful withdrawal
{ status: true, message: 'success', date: '2016-01-30', amount: 35, bills: [20,10,5]}

# Error message - wrong pin
{ status: false, message: 'wrong pin', date: '2016-01-30'}

# Error message - expired card
{ status: false, message: 'card expired', date: '2016-01-30'}
```

## Tips and Tricks

- Don't overdo it. Keep your implementation as simple as possible.
- Test your code - both manually in IRB but, most importantly with automated tests.
- You have to examine all tests ** *before* ** you write your production code. See tests as a blueprint for your implementation.
- **Run one test at the time to avoid a massive output in your terminal.** Disable `it` blocks by adding  an `x` before (as in `xit "Should..." do`)
- Make sure to **commit often** and (if you are Pair Programming) switch navigators (the person that writes code).

This is a good time to read the [Naming Standards](../extras/naming_standards.md) section.


