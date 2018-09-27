## What is Rspec?

A testing framework used for automated tests. It stands for Ruby Specifications. We will use Rspec for the unit test in this  bootcamp, even though there is a possibility to use Rspec for acceptance tests as well. RSpec is written in Ruby but it also uses a DSL (domain-specific language) This means that even though we are using Ruby when writing specs there are also built-in methods in RSpec that does things their own way.

### So what is a spec anyway?

The definition is as follows:

A Spec  (also called test/example) is an executable example that tests whether a portion of code exhibits the expected behavior in a controlled context. In simpler terms this means:

Given some context,

When some event occurs,

Then I should expect some outcome.

Given-When-Then

Do take note that this structure  isverysimilar  to the structure of user stories.

Coincidence? I think not!

## Why should we test?

There are multiple reasons for this but the most important reasons are:

-   Find bugs in the code
    
-   Increases your critical thinking (forces you to think about the code you are writing)
    
-   Cover edge cases (like unexpected use of other languages, numbers etc)
    
-   Increases your confidence (deploying code, adding features, adding gems)
    

There are also some occasions when testing is not recommended or unnecessary.

You should not test basic ruby. This means that you are testing built  in  ruby methods. There are some other instances where testing is unnecessary, and I encourage you to think about what situations there are where testing is unnecessary.


## Installation and configuration

We have covered how to install RSpec in detail during the prep course, so we will keep this short.

There are two ways of installing RSpec, You either run `gem install rspec` in your terminal and then `rspec --init` in your terminal in order for it to create all config file `.rspec` and `spec_helper.rb`

The second way is to add the  rspec  gem to your Gemfile, run `bundle` in your terminal and `rspec --init` once  rspec, is installed.

In order to configure RSpec you can run `rspec --help` in the terminal and it will provide you wit a lot of options that you can use in the `.rspec` file to configure your output from when the test suites run.

## Rspec basics

The first thing we need when we start testing is a spec file for the tests we want to write. The convention dictates that we use the class name of the object we want to test, followed by the  word `_spec`.  Then, of course, we need to give the file a suffix - in this case `.rb` - our specs are written in ruby.

If we want to test a `Car` class, our spec file will be  named `car_spec.rb`.  Inside the spec  file  we will need  to `require` the  file that we are running the test for.

For example, we need to `test car.rb` this means that we need to `require car.rb` in our `car_spec.rb`

car.rb should be in the lib folder and car_spec.rb should be in the spec folder that was created when we ran the command`rspec --init` in the terminal.

### Structure of a test file

Like everything, we need to structure our test in order for them to make sense and for RSpec to understand what we want it to test. We will always have a describe block where we define our class or code. You can have several describe blocks inside another describe block in order for more structure in the file. You can view the describe block as a chapter for a book and  the it  blocks as separate pages in that book. You can also use the word the  keyword `context` when  you want to use a `describe` block inside  another `describe` block, the  keyword `context` is  an alias  for `describe` and  gives more clarity in some situations.

![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2816%29.png)

### Expectations

Expectations are our way to tell RSpec what we want it to test. The structure of an expect statement is  
`expect().to eq()`or `expect().not_to eq()`

There are four parts to an expectation, `expect``expect-argument`, `to` and the `to-argument` (  or `not_to` argument).  
Don't let the lack or presence of the brackets after the to fool you it is still an argument. Ruby gives us the option to skip the brackets if we want.

![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2820%29.png)

### The main structure

The image below shows a chart on the hierarchy on the structure of a spec suite.![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2821%29.png)

This translates to![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2822%29.png)

## Writing Specs

There are many ways to write specs and these depend on what we want to test for

How to describe attributes![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2826%29.png)

How to describe an instance method![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2824%29.png)

How to describe a class method![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2823%29.png)


## Working with expectations

Expectations are the heart of RSpec

The main rule is to only have one expectation  to  each example and the reason for this is that the test suite will stop running if the first expectation fails. If you only have one expectation for every example then the test suites will run independently.

![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2827%29.png)


Some more examples![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2830%29.png)`eq`, `match`, `be` and `match_array` are  the verbs that we have provided and they _are_ the  matchers.

The  matchers  are used as arguments to  the `.to` keywords.

## Different  matchers  for different situations

As always there are many  sitations  where we need specific  matchers  to test situations. And for this RSpec has a lot of  built in  matchers  that we can user depending on those  sitations.

### Equivalence  matchers

Equivalence  matchers  are used to comparing values and variables. These are the most  commenly  used in RSpec. In ruby there are different ways to compare values. And the result varies on how strict you want to be when comparing values. For example 1 and 1.0 are very close but not exactly the same. We have to keep this in mind when using equivalence  matchers.![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2831%29.png)

### Truthiness  matchers

Truthiness  matchers  are used to  determine  if something is either true or false. ![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2832%29.png)

RSpec also lets you determine true or false for other object that are not booleans. For example `nil`would give you a `false` output, and the presence of an object would evaluate to `true`![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2833%29.png)

Now there is no wonder that this could be confusing for the best of us. The people of RSpec thought of that and gave us some options to handle those special cases where something roughly evaluates to true or false. ![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2835%29.png)

### Numeric comparison  matchers

When we want to compare two numeric values we use numeric comparison  matchers. We often use the `be` or `eq` when working with numeric comparison  matchers ![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2836%29.png)

### Collections

RSpec has special  mathcers  for collections like arrays and hashes.![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2837%29.png)

There are also circumstances when we don't care about the order of the elements inside an array![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2838%29.png)

We also have the option to work with strings which are a collection of characters![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2840%29.png)

The last of the collections are hashes.![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2841%29.png)

### Predicate  matchers

Predictive  matchers  are built upon  rubys  predictive methods:

Some of  rubys  predictive methods are:

`.nil? .empty? .zero? .integer?`

We can easily transform these methods to  rspec  matchers  by adding `be_` and removing the `?`![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2844%29.png)

Rspec is smart enough to understand when we are using predictive methods and transforms them to be dynamically used in our expectations. It is also smart enough to use your custom methods (methods that you have created). Imagine that we have created a method called `due_soon?` and we want to test it on `todo`.![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2845%29.png)


## Hooks

We will cover some helper methods in this sections

Hooks help us to keep our code DRY. Instead of writing the same code over and over again we write it once and ask RSpec to run that specific code either before, around or after an example.

Before and after hooks are most commonly used. ![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2846%29.png)

An example on before hooks![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2849%29.png)

Around is a little different than before and after hooks. You can say that around hooks do the job of both before and after hooks at the same time. So why even bother using around when you can use the before and after hook  togheter? The answer is that the around hook executes much faster than the before and after hook  togheter. ![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2847%29.png)

### Let method

The let  method is a great option when you just need variables inside your specs.

Every time you use around, before or after hooks they create new instances of that class for each and every time the spec runs. We have an example below. The before hook creates a new instance of the class for the spec, We also need to assign the new instance as an instance variable.It is also overwritten each time we run the tests. In this case, it's not a big problem. But if we need to bring code from the database then that could become a problem.![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2851%29.png)

In order to solve this problem we can write a piece of code:![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2853%29.png)This  code is however not good and RSpec provides us with a much better option to use which is the let method.

The let method creates a helper method that you can use in your specs. And it also executes once you call it, this is appropriately named _lazy execution. Making the tests run faster and cleaning up your specs.![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2854%29.png)

#### Subject

If we want to reference a single instance variable which is the most common case RSpec lets use another helper method named  subject.![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2855%29.png)


## Test Doubles

So imagine that we need to use an instance of a class to our specs, but we haven't yet created that class. Then we can use a double as a stand-in for that class.

Let us think of it in another way.

During a film shoot, we would need a person with the same height and build of an actor to do stunts because the actor can't do them or he/she is shooting another scene. We would call this person for a stunt double. And this is the same idea with doubles. They are stand-ins for the real objects that are currently not available or we cannot use them for performance reasons.

Some scenarios to use test doubles are

-   When we want to interact with APIs
-   Testing to send out emails (when we don't want to send out actual emails)

Test doubles return known values and fake method implementations.

Some more definitions:

-   Stub: Returns canned responses avoiding computation, mostly used in query methods.
-   Mock: Expects specific messages, will raise errors if it doesn't receive the message by the end of the example.
-   Spy: Records the messages you receive so that you can check them later.

![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2856%29.png)

#### Creating a Stub![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2858%29.png)

Another example:![](https://craftacademy.gitbooks.io/coding-as-a-craft-bootcamp-prep-v2-0/content/assets/carbon%20%2859%29.png)

These two examples do the same thing but written differently.