## Ruby basics - ATM Challenge

### Learning experience
- Learn about Ruby Classes and methods
- Learn about unit testing with Rspec
- Learn how to use `push` and `pop` on an `Array` object
- Learn about naming standards

### The challenge
Our client is a financial institution that wants to allow its customers to withdraw funds from their accounts using an Automatic Teller Machine (ATM). They have turned to us for a prototype of a system with limited functionality. Our job is to write a simple Ruby program that can be run in the Interactive Ruby Shell (IRB).

### Scope
The following objectives must be met:
- An ATM machine can hold up to $1000
- Withdrawal can be cleared only if the person attempting the withdrawal has sufficient funds on his account
- There are only $10-bills in the ATM. Withdrawals for amouts not divisible by 10 must be rejected.

## Tips and Tricks

- Don't overdo it. Keep your implementation as simple as possible.
- Test your code - both manually in IRB but, most importantly with automated tests.
- Try to write tests ** *before* ** you write your production code. See tests as a blueprint for your implementation.


### Naming standards
You are free to set your own class and methods names. 
#### Classes and Modules 
Class and Module names starts with an uppercase letter, by convention they are named using MixedCase, e.g. module Encryption, class MixedCase

#### Methods
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
method returnes true/false) get phrased as questions:
  - `obj.date_today?`
  - `obj.name?`
  - `obj.calculation_done?`
- Methods that modify the object itself, should be
exclamations:
  - `obj.truncate_body!`
  - `obj.remove_name!`

#### Variables

Variables in Ruby can contain data of any type. You can use variables in your Ruby programs without any declarations. Variable name itself denotes its scope (local, global, instance, etc.).

- A local variable (declared within an object) name consists of a lowercase letter (or an underscore) followed by name characters (`balance`, `cyrrent_collection`).
- An instance variable name starts with an `@` sign followed by a name (`@sign`, `@name`, etc).
- A class variable name start with a double `@` sign (`@@`) and may be followed by digits, underscores, and letters, e.g. `@@colour`
- Global variables starts with a dollar ($) sign followed by other characters, e.g. `$global`