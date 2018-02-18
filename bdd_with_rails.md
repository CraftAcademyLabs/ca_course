# Acceptance-Unit Test Cycle

The concept of Acceptance-Unit Test Cycle is pretty simple. You describe what you want the system to do by describing potential users interactions with the different parts of the application. You work outside-in to implement features using the examples to validate that you're building the right thing at the right time. During this workshop, you will see this workflow in action and will experience, at least partially, its benefits.

For the purpose of this workshop, we will use [Ruby on Rails](https://rubyonrails.org/) as the framework for our application. But the workflow can be applied to any stack or framework of your choice.

### Testing\/Development Cycle

A good cycle to follow is this **outside-in approach**:

1. Write a **high-level** \(outside\) business value example \(using Cucumber\) that goes red
2. Write a **lower-level** \(inside\) RSpec example for the first step of implementation that **goes red**
3. Implement the minimum code to pass that lower-level example, see it **go green**
4. Write the next lower-level RSpec example pushing towards passing step 1
5. Repeat steps 3 and 4 until the high-level test \(1\) **goes green**
6. Start over by writing a new **high-level** test

During the process think of your **red-green state** as a permission status:

* When your low-level tests are green, you have permission to write new examples or refactor existing implementation. You must not, in the context of that refactoring, add new functionality\/flexibility.
* When your low-level tests are red, you have permission to write or change implementation code only for the purpose of making the existing tests go green. You must resist the urge to write the code to pass your next test, which doesn't exist, or implement features you’ll need "some day."

The tools we will be using to test our application are [RSpec](http://rspec.info/) and [Cucumber](https://cucumber.io/). Those are the two testing frameworks that will help us to write good code. By now, you've had a chance to use both tools in your previous projects, so this is not entirely new to you.

#### Prerequisites

We assume that you have your Development environment setup according to instructions in this book and that you are working on either a Linux or OSX based computer.

### Setting up the application

If you don't have Rails installed beforehand, it is time to install it now. Rails is a ruby gem, so you can install it as any other gem:

```shell
$ gem install rails
```

Once you are done it is time to scaffold the Rails application we will be working with.

```shell
$ rails new rails_demo --database=postgresql --skip-test --skip-bundle
```

* The `--database=postgresql` selects PostgreSQL as the database \(The out-of-the-box setting is MySQL\)
* The `--skip-test` option skips configuring for the default testing tool.
* The `--skip-bundle` option prevents the generator from running `bundle install` automatically.

```
$ cd rails_demo
```

### RSpec and shoulda-matchers

Add `rspec-rails` gem to the `development` and `test` groups of your Gemfile.

The RSpec extension library `shoulda-matchers` allows us to test common Rails functionality, like validations and associations, with less code.

The gem `factory_bot` is a library for setting up Ruby objects as test data. It's essentially a fixtures replacement. It allows you to create objects that are needed in tests without providing a value for each required attribute. If you don't provide a value for a required attribute factory\_girl will use the default value that you defined in factory's definition.

```ruby
group :development, :test do
  gem 'factory_bot_rails'
  gem 'pry-byebug'
  gem 'pry-rails'
  gem 'rspec-rails'
  gem 'shoulda-matchers'
end
```

Now, we can install all the new dependencies with `bundle install`.

```shell
$ bundle install
```

Run the RSpec generator to add the testing framework to your rails application

```shell
$ bundle exec rails generate rspec:install
```

Why `bundle exec` before a command? `bundle exec` executes a command in the context of your bundle. That means it uses the gems specified in your Gemfile. It basically standardizes the environment under which the program runs. This helps avoid version hell and makes life much easier.

Before you can use `shoulda-matchers`, you need to configure it by choosing the test framework and features of `shoulda-matchers` you want to use.

Open `spec/rails_helper.rb` and add the following block at the end of the file.

```ruby
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails 
  end
end
```

Before we move on we need to add another configuration to the Rails application to avoid the generators to scaffold too many files. Modify the `Application` class in the `config/application.rb` file to have the following code:

```ruby
class Application < Rails::Application
  config.load_defaults 5.1

  # Disable generation of helpers, javascripts, css, and view, helper, routing and controller specs
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
```

One last thing in this setup process, open the `.rspec` file \(it is located in your main project folder but it is a hidden file, so your text editor might not show it\) and modify it so that the first line is set to:

```shell
--format documentation
```

Now, in your terminal, type in `bundle exec rspec` and hit enter.The output you see should be something like:

```shell
$ bundle exec rspec
No examples found.

Finished in 0.00023 seconds (files took 0.5029 seconds to load)
0 examples, 0 failures
```

### Cucumber

Add `cucumber-rails` and `database_cleaner` gems to the test group of the Gemfile.

```ruby
group :development, :test do
  gem 'cucumber-rails', require: false
  gem 'database_cleaner'
  # ... All the gems that were previously in this block
end
```

The `database_cleaner` gem is used to ensure a clean database state for testing and will make your development easier.

Run this command to bootstrap the application with Cucumber:

```shell
$ bundle exec rails generate cucumber:install
```

This will generate Cucumber configuration files and set up the database for Cucumber tests. The generator will also create all the code you need to integrate [DatabaseCleaner](https://github.com/DatabaseCleaner/database_cleaner#cucumber-example) into your Rails project.

As a last step, you will need to create the database and run the `migrate` command \(even if we have not created any tables and columns yet\)

```shell
$ rails db:create --all
$ rails db:migrate
```

Just as a sanity check, we want to run Cucumber to see if everything is working properly.

```shell
$ bundle exec cucumber
Using the default profile...
0 scenarios
0 steps
0m0.000s
Run options: --seed 44264

# Running:


Finished in 0.001691s, 0.0000 runs/s, 0.0000 assertions/s.

0 runs, 0 assertions, 0 failures, 0 errors, 0 skips
```

You should now be fully equipped to drive your Rails application using the Acceptance-Unit Test Cycle. Next up, we will go through the cycle of developing a feature using this approach.

> **Note:** As in all other projects during the course we will be storing our work using git. Initialize a git repository inside the application's directory and remember _**you should commit early and often**_

### Our first Acceptance-Unit Test Cycle

Let's develop a simple feature. In the scenario, a user will visit the application's landing page and see a list of news articles displayed.

Let's start by creating a high-level test file.

`features/list_articles.feature`

```gherkin
Feature: List articles on the landing page
  As a visitor,
  When I visit the landing page of the application
  I would like to see a list of all articles

  Scenario: View list of articles on the landing page
    When I visit the site
    Then I should see "A breaking news item"
    And I should see "Some really breaking action"
```

Run the scenario and see it fail.

```shell
$ bundle exec cucumber features/list_articles.feature
```

In the terminal output, you will see snippets for implementing steps:

```shell
You can implement step definitions for undefined steps with these snippets:

When("I visit the site") do
  pending # Write code here that turns the phrase above into concrete actions
end

Then("I should see {string}") do |string|
  pending # Write code here that turns the phrase above into concrete actions
end
```

Let's copy those steps into `features/step_definitions/landing_page_steps.rb` and edit them:

```ruby
When("I visit the site") do
  visit root_path
end

Then("I should see {string}") do |content|
  expect(page).to have_content content
end
```

If you run the scenario again, you will see that it fails since the route `root_path` is not defined. Let's add it to our `routes.rb` file:

`config/routes.rb`

```ruby
Rails.application.routes.draw do
  root controller: :articles, action: :index
end
```

Now implement the controller that will serve the `/` route. For that, we will use a Rails Generator.

```shell
$ rails generate controller Articles index
      create app/controllers/articles_controller.rb
      route get 'articles/index'
      invoke erb
      create app/views/articles
      create app/views/articles/index.html.erb
      invoke rspec
```

Now that we have the `root_path` setup with the LandingController, let's run our tests again. The first step of our scenario is now green.![](/bdd_with_rails/01_first_test_green.png)

Let's get the remaining steps to a green state too. Looking at the previous output of cucumber, we note that it expected to see some text on the page, but didn't find any that was specified. It also points us to the file that was supposed to render the text in question `app/views/landing/index.html.erb`.

All we need to do at this stage to get the remaining tests to pass is add the respective texts to that view file. Edit `app/views/landing/index.html.erb` and replace the file's content with:

```html
<p>A breaking news item</p>
<p>Some really breaking action</p>
```

Run cucumber now and see all the test pass.

Great! all our steps are now green. We are not quite done yet though. If we take a look at our user story:

```
As a visitor,
When I visit the landing page of the application
I would like to see a list of all articles
```

We want to list a number of articles on our landing page. All we managed to do so far is display two static texts on our page. Let's update our cucumber scenario to clearly reflect that:

```gherkin
Feature: List articles on the landing page
  As a visitor,
  When I visit the landing page of the application
  I would like to see a list of all articles

  Scenario: View list of articles on the landing page
    Given the following articles exists
      | title                | content                          |
      | A breaking news item | Some really breaking action      |
      | Learn Rails 5        | Build awesome rails applications |
    When I visit the site
    Then I should see "A breaking news item"
    And I should see "Some really breaking action"
    And I should see "Learn Rails 5"
    And I should see "Build awesome rails applications"
```

We've now added a step that will set the stage for our scenario by creating two articles in our database. We then check to see that both articles are displayed on our landing page. If we run cucumber now, we get an undefined step with the following code snippet:

```shell
Given("the following articles exists") do |table|
  # table is a Cucumber::MultilineArgument::DataTable
  pending # Write code here that turns the phrase above into concrete actions
end
```

Let's add that to our step definitions and update the code of that step to as follow:

```ruby
Given("the following articles exists") do |table|
  table.hashes.each do |article|
    Article.create!(article)
  end
end
```

Now run the feature file and see a new error message:

```shell
...

uninitialized constant Article (NameError) 
...

Failing Scenarios:
cucumber features/list_articles.feature:12 # Scenario: Viewing list of articles on application's landing page

1 scenario (1 failed)
6 steps (1 failed, 5 skipped)
0m0.155s
```

**This is a very crucial step in our development process.** This is as far as you can go in the high-level tests - at this point, we need to shift our attention to the process of creating our units \(Article\) using tests as a blueprint. It's time to focus on the **lower-level** \(inside\) tests using RSpec.

Let's think about how we want our Articles to be structured:

* We need to be able to store Articles in our database - meaning we need to create a Model
* We want each article to have a `title` and `content` - we need to add these attributes to our Article model
* We don't want to store articles that don't have `title` and `content` present - meaning we need to add a validation that these attributes are not empty
* We want to make sure that there is a valid Factory to use in our test environment

The first thing we want to do is to create a spec file in the `spec/models` folder called `article_spec.rb`. Let's add some tests \(we will use the matchers provided to us by the `shoulda-matchers` gem that extends the built-in RSpec matchers\).

`spec/models/article_spec.rb`

```ruby
require 'rails_helper'

RSpec.describe Article, type: :model do
  describe 'DB table' do
    it { is_expected.to have_db_column :id }
    it { is_expected.to have_db_column :title }
    it { is_expected.to have_db_column :content }
  end

  describe 'Validations' do
    it { is_expected.to validate_presence_of :title }
    it { is_expected.to validate_presence_of :content } 
  end

  describe 'Factory' do
    it 'should have valid Factory' do
      expect(FactoryBot.create(:article)).to be_valid 
    end
  end
end
```

If you run RSpec \(and you should\) now you will see the tests fail - we haven't created the model yet.

It's time to do that. We'll make use of another rails generator to create the Article model. That will create the class, a migration and a factory \(if you are prompted to overwrite the `article_spec.rb`just type `n`\)

```shell
$ rails generate model Article title:string content:text
```

Following that, you need to apply the migration to update your database.

```shell
$ rails db:migrate db:test:prepare
== 20160725233007 CreateArticles: migrating ===================================
-- create_table(:articles)
   -> 0.0295s
== 20160725233007 CreateArticles: migrated (0.0296s) ==========================
```

Running the spec now you will see that we are moving in the right direction but there are still some failing tests - we have not set up any validations and those assertions are giving us errors.

Open the model file and add validation for the presence of both `title` and `content`.

`app/models/article.rb`

```ruby
class Article < ApplicationRecord
  validates :title, presence: true
  validates :content, presence: true
end
```

Running your specs now \(`rspec`\) will result in all green tests.

At this point, we can go back to our acceptance test and run Cucumber again. We are still not in the green, but we should have a different error message.

Now, we need to modify the controller as well as the view to actually display \(or try to display\) our articles.

```ruby
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end
end
```

And replace the content of the view that will render `ArticlesController#index` action and list all posts.

```erb
<% @articles.each do |article| %>
  <p><%= article.title %></p>
  <p><%= article.content %></p>
<% end %>
```

At this point, if you run `cucumber` you should see all tests passing green.

#### Wrap up

During this walkthrough, we have completed one Acceptance-Unit Cycle \(without the refactoring part\) and added a simple feature that allows visitors to view articles on the landing page of the application.

Using our tests we were able to craft out some functionality and delivered the objective of the feature: To list articles on the landing page.

* We created a route \(URL\)
* We created an Article model with attributes and validations
* We created a controller with an index method that fetches all articles from the database and stores the collection in a variable that is made available to the view template
* We created a view that iterates through a collection of articles

In the next cycle, we would certainly add more scenarios to this feature to test other paths. What should happen when there are no articles in the system? Will multiple articles be displayed correctly? Etc..

**It takes a little practice but with this approach you are constantly in charge of the workflow and know what the next step of your implementation should be.**

