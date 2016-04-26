## Naming standards
You are free to set your own class and methods names. 
### Classes and Modules 
Class and Module names starts with an uppercase letter, by convention they are named using `MixedCase`, e.g. `module Withdraval`, `class AccountHolder`.

### Methods

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

### Variables

Variables in Ruby can contain data of any type. You can use variables in your Ruby programs without any declarations. Variable name itself denotes its scope (local, global, instance, class.).

- A local variable (declared within an object) name consists of a lowercase letter (or an underscore) followed by name characters (`balance`, `cyrrent_collection`).
- An instance variable name starts with an `@` sign followed by a name (`@sign`, `@name`, etc).
- A class variable name start with a double `@` sign (`@@`) and may be followed by digits, underscores, and letters, e.g. `@@colour`
- Global variables starts with a dollar ($) sign followed by other characters, e.g. `$global`