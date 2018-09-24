# Setting up
Let's get back to the ATM challenge. Before we s
### Learning objectives

1. Learn how to set up a simple ruby based project
2. Make use of a testing framework 
3. Connect your project (local repository) to a GitHub repository
4. Practice Pair Programing and sharing code

### Action points

* Set up a simple project   
    1. Create a `Gemfile` and add `rspec` as a dependency.
    2. Initiate `rspec`
    3. Modify `.rspec` to output the spec messages in terminal.
* Create a basic folder structure for your project (production code in `lib`, tests in `spec`)
* Create a `README.md` with a basic description of the project
* Initialize and push a git repository
* Add your Pair as a `git remote`

### Walkthrough

Create a project folder in your Terminal and `cd` into that folder

```shell
$ touch atm_challenge
```
The first step is to add RSpec as a dependency for the project. 

Inside the project folder, create a `Gemfile` and modify it's content

```ruby
# Gemfile

source 'https://rubygems.org'

gem 'rspec'
```

Back it your Terminal, run `bundle` to install the dependency. 

Open the project in Atom. You can do that directly from your Terminal

```shell
$ atom .
```

You should see a new file in the projects file and folder structure called `Gemfile.lock`. Open it and examine its content. You can see that by running bundle, you have installed the gem `rspec` but also all the gems that RSpec in turn depends on.



