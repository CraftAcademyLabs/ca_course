---
title: "Ruby basics - ATM Challenge"
author: [Craft Academy]
date: Version 2.1 - 2018
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

Welcome to your first Ruby assignment. We will start the next step on your journey toward becoming a programmer/developer/coder by building a small piece of software that is suppposed to simulate an ATM (or as we call it in swedish: Bankomat)



![Bankomat](https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/En_automat_fr%C3%A5n_Bankomat.jpg/250px-En_automat_fr%C3%A5n_Bankomat.jpg)

Withdrawing money from an ATM is fairly common, everyday action. We've all done it at some point, right? You are in need of some cash (for whatever reason), you have your Visa or MasterCard with you, you spot an ATM machine, you walk up to it, insert your card, enter your pin and you grab a few bucks or kronor or what have you. But what is exactly happening in this situation, or rather this particular interaction between you and that piece of machine? 


The characteristics of this interaction makes it a good first step into understanding how software works, and how we can understand requirements and turn them into features.

This is not a walk in the park. Just becouse the problem set in itself does not seem overly complicated, doesn't make it easy to translate into executable code. You will need to stay alert, employ everything you've learned so far and work hard if you want to compleate this challege. 

Work hard, pay attention to details and collaborate with your fellow students, and you'll be all good. 


## Learning objectives

- Review fundamentals learned in the prep course
- Learn about Ruby classes, modules, methods and attributes
- Learn about unit testing with RSpec and the benefits of writing automated tests
- Learn about naming standards
- Learn about using double, class_double and instance_double
- Learn about debugging and common programming techniques

## The challenge

Our client is a financial institution that wants to allow its customers to withdraw funds from their accounts using an Automatic Teller Machine (ATM). They have turned to us for a prototype of a system with limited functionality. Our job is to write a simple Ruby program that can be run in the Interactive Ruby Shell (IRB).

**You will work with your Coach and your peers to get started with using RSpec as a testing framework, and with implementing your code.**



## Scope
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

### Example output
```ruby
# Message on successful withdrawal
{ status: true, message: 'success', date: '2016-01-30', amount: 35, bills: [20,10,5]}

# Error message - wrong pin
{ status: false, message: 'wrong pin', date: '2016-01-30'}

# Error message - expired card
{ status: false, message: 'card expired', date: '2016-01-30'}
```

# Tips and Tricks

- **Don't overdo it!** Complexity kills good software. And it programmers ;-).  Keep your implementation as simple as possible.
- **Test your code** - both manually in IRB but, most importantly with automated tests. You have to examine all tests **_before_** you write your production code. See tests as a blueprint for your implementation.
- **Run one test at the time to avoid a massive output in your terminal.** Run just the test/spec you are currently working on by pointing to a file and adding the line number (e.g. `rspec spec/atm_spec.rb:12`) Also, you can disable `it` blocks by adding an `x` before the `it` (as in `xit "Should..." do`). 
- Make sure to **commit often** and (if you are Pair Programming) switch navigators (the person that writes code).

# Naming standards

Before we move on with the challenge, we need to point out a few things about Naming Standards. Junior developers often struggle to choose good names for variables and methods they write. All entities (classes, objects, methods, variables, etc...) in our code can be described by different names. It’s important to choose one that represents the entity best for the current context. You ARE free to set your own class and methods names, but following conventions and common sense is calld for. There are also some constraints in Ruby concerning names, so please take a close look at the sections below.  

## Classes and Modules

Class and Module names starts with an uppercase letter, by convention they are named using `CamepCase` (also called `PascalCase`), e.g. `module Withdraval`, `class AccountHolder`.

## Methods

When naming methods the goal is to be descriptive but short. Name based on what it will return or what the major intended side effect will be. You shouldn't be missing any parts from the name because the method should only do one thing anyway. If you can't tell what the method will return based on the name, you probably need a better name. If your method name seems insanely long, your method may be trying to do more than one thing. End with a question mark `?` if it will return `true`/`false`.


Method names should begin with a lowercase letter (or an underscore). Apart from letters, only `?`, `!` and `=` characters are allowed as method name suffixes. This is a list of suggestions on naming standards for different kind of methods:

- Methods that do something should be verbs:
  - `obj.calculate`
  - `obj.set_name`
  - `obj.get_date`
- Methods that are accessors (or behave like them) should be nouns:
  - `foo = obj.name`
  - `obj.date`
  - `obj.order_total`
- Interrogative methods (if the
method returns true/false) get phrased as questions:
  - `obj.date_today?`
  - `obj.name?`
  - `obj.calculation_done?`
- Methods that modify the object itself, should be
exclamations:
  - `obj.truncate_body!`
  - `obj.remove_name!`

## Variables

Variables in Ruby can contain data of any type. You can use variables in your Ruby programs without any declarations. Variable name itself denotes its scope (local, global, instance, class.).

- A local variable (declared within an object) name consists of a lowercase letter (or an underscore) followed by name characters (`balance`, `cyrrent_collection`).
- An instance variable name starts with an `@` sign followed by a name (`@sign`, `@name`, etc).
- A class variable name start with a double `@` sign (`@@`) and may be followed by digits, underscores, and letters, e.g. `@@colour`
- Global variables starts with a dollar (`$`) sign followed by other characters, e.g. `$global`


