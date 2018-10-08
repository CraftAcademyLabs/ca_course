---
title: "Ruby On Rails - Setup"
subtitle: "Training Wheels"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Testing, Rails"
keywords: [Testing, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...


# Rails with Testing frameworks

This guide will provide a step-by-step instruction to scaffold a new Rails 5.2 application. We will be using a separate gemset to contain the dependencies used in this project. You should familiarize yourself with the concept of gemsets by reading more about them on the [RVM documentation - (https://rvm.io/gemsets/basics)](https://rvm.io/gemsets/basics) site. 

_Please note that this setup may or may not suite your purposes - the frameworks we use are the one that suites us and makes our workflow effective and productive. But it's up to you to form your own opinion._ 

## Workflow

In the beginning of the project, you should focus on splitting everything into small chores. Make sure to create **CHORES** for each user story we present below. 

### Set up main repository

**Description**

```gherkin
As a development team
In order to be able to collectively work on the same codebase
We would like to have a main repository on GitHub we all can use as the upstream repository
```

**Tasks (Acceptance Criteria)**

- [ ] Set up a repository on GitHub (Please treat your own account as the account for this organization for the sake of this exercise)
- [ ] Create a `development` branch and set it as the main branch of the project
- [ ] Share repo with team

### Set up application structure

**Description**

```gherkin
As a development team
In order to be able to start adding features
We would like to have an basic application structure 
```

**Tasks (Acceptance Criteria)**

- [ ] Scaffold a Rails application
- [ ] Turn off generators for helpers, assets and all specs (accept model specs)
- [ ] Clean up the generated code from unnecessary comment for good readability


### Set up testing environment

**Description**

```gherkin
As a development team
In order to be able to make sure our code is doing what it is supposed to do
We would like to be able to write and run automated tests
```

**Tasks (Acceptance Criteria)**

- [ ] Add and configure Cucumber for Acceptance tests
- [ ] Add and configure Rspec for Unit and Request specs


### Set up Continious Ingegration

**Description**

```gherkin
As a development team
In order to make sure that the code we add to the code base is functional
We would like to run full test suite on a remote service
```

**Tasks (Acceptance Criteria)**

- [ ] Set up CI on Semaphore or Travis

### Set up Test Coverage Metrics
**Description**

```gherkin
As a development team
In order to see how much of our code is covered in tests
We would like to measure test coverage using a remote service 
```

**Tasks (Acceptance Criteria)**

- [ ] Set up Coveralls for the main repository
- [ ] Add and configure Coveralls for RSpec and Cucumber
- [ ] Coverage results needs to be merged in order to get metrics from both tools (add a Rake task - see the Coveralls guide).

### Set up Continious Deployment
**Description**

```gherkin
As a development team
In order to automate the deployment process during development
We would like all code that has been merged into the main code base, to be deployed to a server
```

**Tasks (Acceptance Criteria)**

- [ ] Create an application on Heroku
- [ ] Set up deployment using the CI tool

## New app using Rails 5.2.0

```
$ rvm gemset create rails_5_2
$ rvm gemset use rails_5_2
$ gem install rails
$ rails new my_project --database=postgresql --skip-test --skip-bundle
$ cd news_room
```

* The --database=postgresql selects PostgreSQL as the database (The out-of-the-box setting is MySQL)
* The --skip-test option skips configuring for the default testing tool.
* The --skip-bundle option prevents the generator from running bundle install automatically.

Make sure to `cd` into the new project folder that the `rails new` command created for us. 

```
$ touch .ruby-gemset
```
Inside that file, we'll set the name of the gemset we want to use. This will automatically switch to the right gemset when you cd into the project folder.

```
rails_5_2
```

### HAML
In order to use Haml in your view templates, add `gem haml-rails` to your `Gemfile`. 

### Testing Frameworks and tools

Add `rspec-rails` gem to the development and test groups of your Gemfile.

The RSpec extension library `shoulda-matchers` allows us to test common Rails functionality, like validations and associations, with less code.

The gem `factory_bot_rails` is a library for setting up Ruby objects as test data. It's essentially a fixtures replacement. It allows you to create objects that are needed in tests without providing a value for each required attribute. If you don't provide a value for a required attribute `factory_bot` will use the default value that you defined in factory's definition.

[Pry](https://github.com/pry/pry) is a powerful alternative to the standard IRB shell for Ruby. [`pry-rails`](https://github.com/rweng/pry-rails) causes Rails console to open pry. [`pry-byebug`](https://github.com/deivid-rodriguez/pry-byebug) adds step-by-step debugging and stack navigation capabilities to pry. 


```ruby
group :development, :test do
  gem 'factory_bot_rails'
  gem 'pry-byebug'
  gem 'pry-rails'
  gem 'rspec-rails'
  gem 'shoulda-matchers'
end
```

### Configuration

```ruby
module MyProject
  class Application < Rails::Application
    config.load_defaults 5.2
    config.generators do |generate|
      generate.helper false
      generate.assets false
      generate.view_specs false
      generate.helper_specs false
      generate.routing_specs false
      generate.controller_specs false
      generate.system_tests false
    end
  end
end
```

Open `spec/rails_helper.rb` and add the following block at the end of the file to configure ShouldaMatchers.

```ruby
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails 
  end
end
```

I also like to include FactoryBot methods in my RSpec config to be able to use shorter syntax in my specs:

```ruby
RSpec.configure do |config|
  # other settings 
  config.include FactoryBot::Syntax::Methods
end
```

Add an Acceptance test framework

```ruby
group :development, :test do
  # Add Cucumber and Database Cleaner
  gem 'cucumber-rails', require: false
  gem 'database_cleaner'
end
```

Configure testing frameworks with default settings
```bash
$ bundle exec rails generate cucumber:install
$ bundle exec rails generate rspec:install
```

As we did with RSpec, we would like to include FactoryBot methods in my Cucumber configuration file (`features/support/env.rb`), to be able to use shorter syntax in my step definitions:

```ruby 
World(FactoryBot::Syntax::Methods)
```

```bash
$ rails db:create db:migrate
```

Run your tests to check if all setup is working:

```bash
$ rake
```

## Wrap up

You should get a green response from both testing frameworks. 
If all is good, commit and push. At this stage, you are ready to get started with implementing your features - BDD style.

Good luck with your new Rails application