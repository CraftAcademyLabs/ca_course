# Acceptance-Unit Test Cycle 

The concept of Acceptance-Unit Test Cycle is pretty simple. You describe what you want the system to do by describing potential users interactions with the different parts of the application. You work outside-in to implement features using the examples to validate that you're building the right thing at the right time. During this workshop you will see this workflow in action and will experience, at least partially, its benefits.

For the purpose of this workshop, we will use [Ruby on Rails](https://rubyonrails.org/) as the framework for our application. But the workflow can be applied to any stack or framework of your choice.

###Testing/Development Cycle

A good cycle to follow is this **outside-in approach**:

1. Write a **high-level** (outside) business value example (using Cucumber) that goes red
2. Write a **lower-level** (inside) RSpec example for the first step of implementation that **goes red**
3. Implement the minimum code to pass that lower-level example, see it **go green**
4. Write the next lower-level RSpec example pushing towards passing step 1
5. Repeat steps 3 and 4 until the high-level test (1) **goes green**
6. Start over by writing a new **high-level** test

During the process think of your **red-green state** as a permission status:
- When your low-level tests are green, you have permission to write new examples or refactor existing implementation. You must not, in the context of that refactoring, add new functionality/flexibility.
- When your low-level tests are red, you have permission to write or change implementation code only for the purpose of making the existing tests go green. You must resist the urge to write the code to pass your next test, which doesn't exist, or implement features you’ll need "some day."

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

* The `--database=postgresql` selects PostgreSQL as the database (The out-of-the-box setting is MySQL)
* The `--skip-test` option skips configuring for the default testing tool.
* The `--skip-bundle` option prevents the generator from running `bundle install` automatically.

```
$ cd rails_bdd
```

### RSpec and shoulda-matchers

Add `rspec-rails` gem to the `development` and `test` groups of your Gemfile.

The RSpec extension library [`shoulda-matchers`](http://matchers.shoulda.io/) allows us to test common Rails functionality, like validations and associations, with less code.

The gem `factory_girl` is a library for setting up Ruby objects as test data. It's essentially a fixtures replacement. It allows you to create objects that are needed in tests without providing a value for each required attribute. If you don't provide a value for a required attribute factory_girl will use the default value that you defined in factory's definition.

```ruby
group :development, :test do
  gem 'rspec-rails'
  gem 'shoulda-matchers'
  gem 'factory_girl_rails'
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

Open `spec/rails_helper.rb` and add the following block.

```ruby
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails 
  end
end
```

Before we move on we need to add another configuration to the Rails application to avoid the generators to scaffold too many files. Make the following modification to the `config/application.rb` file:

```ruby
class Application < Rails::Application
  # Disable generation of helpers, javascripts, css, and view, helper, routing and controller specs
  config.generators do |generate|
    generate.helper false 
    generate.assets false
    generate.view_specs false
    generate.helper_specs false
    generate.routing_specs false
    generate.controller_specs false
  end
  # ...
```

One last thing in this setup process, open the `.rspec` file (it is located in your main project folder but it is a hidden file, so your text editor might not show it) and modify it so that the first line is set to:

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
  gem 'rspec-rails'
  gem 'shoulda-matchers'
  gem 'factory_girl_rails'
  gem 'cucumber-rails', require: false
  gem 'database_cleaner'
end
```

The [`database_cleaner`](https://github.com/DatabaseCleaner/database_cleaner) gem is used to ensure a clean database state for testing and will make your development easier.

Run this command to bootstrap the application with Cucumber:

```shell
$ bundle exec rails generate cucumber:install
```

This will generate Cucumber configuration files and set up the database for Cucumber tests. The generator will also create all the code you need to integrate [DatabaseCleaner](https://github.com/DatabaseCleaner/database_cleaner#cucumber-example) into your Rails project.

As a last step, you will need to create the database and run the `migrate` command (even if we have not created any tables and columns yet)

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

You should now be fully equiped to drive your Rails application using the Acceptance-Unit Test Cycle. Next up, we will go through the cycle of developing a feature using this approach.


> **Note:** As in all other projects during the course we will be storing our work using git. Initialize a git repository inside the application's directory and remember ***you should commit early and often***


### Our first Acceptance-Unit Test Cycle

Let's develop a simple feature. In the scenario, a user will visit the applications landing page and see a list of news articles displayed.

Let's start by creating a high-level test file.

```gherkin
Feature: As a visitor, 
         when I visit the applications landing page,
         I would like to see a list of articles
 
  Scenario: Viewing list of articles on application's landing page
    When I am on the landing page
    Then I should see the "A breaking news item"
    And I should see the "Some really breaking action"
```

Run the scenario and see it fail.

```shell
$ bundle exec cucumber features/list_posts.feature
```

In the terminal output, you will see snippets for implementing steps:

```shell
You can implement step definitions for undefined steps with these snippets:

When(/^I am on the landing page$/) do
  pending # Write code here that turns the phrase above into concrete actions
end

Then(/^I should see the "([^"]*)"$/) do |arg1|
  pending # Write code here that turns the phrase above into concrete actions
end
```

Let's copy those steps into `features/step_definitions/landing_page_steps.rb` and edit them:

```ruby
When(/^I am on the landing page$/) do
  visit root_path
end

Then(/^I should see the "([^"]*)"$/) do |content|
  expect(page).to have_content content
end
```

If you run the scenario again, you will see that it fails since the route `root_path` is not defined. Let's add it to our `routes.rb` file:

```ruby
Rails.application.routes.draw do
  root controller: :landing, action: :index
end
```

Now implement the controller that will serve the `/` route. For that we will use a Rails Generator.

```shell
$ rails generate controller landing index
      create app/controllers/landing_controller.rb
      route get 'landing/index'
      invoke erb
      create app/views/landing
      create app/views/landing/index.html.erb
      invoke rspec
```

Running our tests will result in a changed error message. That's progress.

Now, we need to modify the controller as well as the view to actually display (or try to display) our articles.
```ruby
class LandingController < ApplicationController
  def index @articles = Article.all
  end
end
```

And create the view that will render Posts#index action and list all posts.

```erb
<ul> 
  <% @articles.each do |article| %>
    <li>
      <%= article.title %><br />
      <%= article.content %>
    </li>
  <% end %>
</ul>
```

Now run the feature file and see a new error message:

```shell
uninitialized constant LandingController::Article (NameError)
...

Failing Scenarios:
cucumber features/list_posts.feature:3 # Scenario: Viewing list of articles on application's landing page

1 scenario (1 failed)
2 steps (1 failed, 1 skipped)
```

**This is a very crucial step in our development process.** This is as far as you can go in the high level tests - at this point we need to shift our attention to the process of creating our units (Article) using tests as a blueprint. It's time to focus on the **lower-level** (inside) tests using RSpec.

Let's think about how we want our Articles to be structured:

* We need to be able to store Articles in our database - meaning we need to create a Model
* We want each article to have a `title` and `content` - we need to add these attributes to our Article model
* We don't want to store articles that don't have `title` and `content` present - meaning we need to add a validation that these attributes are not empty
* We want to make sure that there is a valid Factory to use in our test environment

The first thing we want to do is to create a spec file in the `spec/models` folder called `article_spec.rb`. Let's add some tests (we will use the matchers provided to us by the `shoulda-matchers` gem that extends the built-in RSpec matchers).

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
      expect(FactoryGirl.create(:article)).to be_valid 
    end
  end
end
```

If you run RSpec (and you should) now you will see the tests fail - we haven't created the model yet.

It's time to do that. We'll make use of another rails generator to create the Article model. That will create the class, a migration and a factory (if you are prompted to overwrite the `atricle_spec`just type `n`)

###Are we good?
Let's create an example Post model and write specs that will verify that RSpec is working correctly:
```
$ bundle exec rails generate model Post title:string content:text
  invoke  active_record
  create    db/migrate/20160322065708_create_posts.rb
  create    app/models/post.rb
  invoke    rspec
  create      spec/models/post_spec.rb
  invoke      factory_girl
  create        spec/factories/posts.rb
```
Notice, the generator also creates a model spec and a Post factory. That's the reason why we included the `rspec-rails` and `factory_girl_rails` gems in the development group of the Gemfile.

Create the database and run the migration that will add the new posts table to the database:
```
$ bundle exec rake db:create
$ bundle exec rake db:migrate
```
A basic post factory has been defined for you and you can modify it if you like:

!FILENAME spec/factories/posts.rb
```ruby
FactoryGirl.define do
  factory :post do
    title "My first post"
    content "Hello, behavior-driven development world!"
  end
end
``` 
Update the spec to test the db table, the Factory and the validations of post's title and content.

!FILENAME spec/models/post_spec.rb
```ruby 
require 'rails_helper'

RSpec.describe Post, type: :model do
  describe 'DB table' do
    it { is_expected.to have_db_column :id }
    it { is_expected.to have_db_column :title }
    it { is_expected.to have_db_column :content }
  end
  
  describe 'Validations' do
    it { is_expected.to validate_presence_of(:title) }
    it { is_expected.to validate_length_of(:title).is_at_least(5) }
    it { is_expected.to validate_presence_of(:content) }
    it { is_expected.to validate_length_of(:content).is_at_least(10) }
  end
  
  describe 'Fixtures' do
    it 'should have valid Factory' do
      expect(FactoryGirl.create(:post)).to be_valid
    end
  end
end
```
Head over to your Terminal and run the `rspec` command. You will see that some of the test have passed and some have gone red - failed.

```
Post
  DB table
    should have db column named id
    should have db column named title
    should have db column named content
  Validations
    should validate that :title cannot be empty/falsy (FAILED - 1)
    should validate that the length of :title is at least 5 (FAILED - 2)
    should validate that :content cannot be empty/falsy (FAILED - 3)
    should validate that the length of :content is at least 10 (FAILED - 4)
  Fixtures
    should have valid Factory

Failures:
...
```
We have failing specs that concern the validations. We haven't defined them yet.
Update the Post model with validation definitions.

!FILENAME app/models/post.rb
```ruby
class Post < ActiveRecord::Base
  validates :title, presence: true, length: { minimum: 5 }
  validates :content, presence: true, length: { minimum: 10 }
end
```

Please notice that after you've added the length validation on `:content`  your post factory might fail when you run RSpec. 
```
Failure/Error: expect(FactoryGirl.create(:post)).to be_valid

ActiveRecord::RecordInvalid:
 Validation failed: Content is too short (minimum is 10 characters)
```
Why is that and how can we fix it?

###Cucumber

Add `cucumber-rails` and `database_cleaner` gems to the test group of the Gemfile.

!FILENAME Gemfile
```ruby
group :development, :test do
  gem 'rspec-rails'
  gem 'shoulda-matchers'
  gem 'factory_girl_rails'
  gem 'cucumber-rails', require: false
  gem 'database_cleaner'
end
```
The `database_cleaner` gem will make your development easier. It's used to ensure a clean database state for testing.

Bootstrap the application with Cucumber:
```
$ bundle exec rails generate cucumber:install
```
This will generate Cucumber configuration files and set up database for Cucumber tests.

###Is Cucumber working correctly?
To make sure of that, let's develop a simple feature. In the scenario, a user will visit the homepage and see that a post is displayed:

!FILENAME features/home_page.feature
```gherkin
Feature: Home page

Scenario: Viewing application's home page
Given there's a post titled "Intro to BDD" with "BDD rocks!" content
When I am on the homepage
Then I should see the "Intro to BDD" post
```

Run the scenario and see it fail.
```
$ bundle exec cucumber features/home_page.feature
```

In the terminal output, you will see snippets for implementing steps:
```
You can implement step definitions for undefined steps with these snippets:

Given(/^there's a post titled "(.*?)" with "(.*?)" content$/) do |arg1, arg2|
  pending # express the regexp above with the code you wish you had
end

When(/^I am on the homepage$/) do
  pending # express the regexp above with the code you wish you had
end

Then(/^I should see the "(.*?)" post$/) do |arg1|
  pending # express the regexp above with the code you wish you had
end
```
Let's copy those steps into `features/step_definitions/home_page_steps.rb` and edit them:

!FILENAME features/step_definitions/basic_steps.rb
```ruby
Given(/^there's a post titled "(.*?)" with "(.*?)" content$/) do |title, content|
  post = FactoryGirl.create(:post, title: title, content: content)
end

When(/^I am on the homepage$/) do
  visit "/"
end

Then(/^I should see the "(.*?)" post$/) do |title|
  post = Post.find_by_title(title)
  expect(page).to have_content(post.title)
  expect(page).to have_content(post.content)
end
```
In these steps we create a post using `factory_girl`, visit the homepage and check if the post is displayed.

If you run the scenario again, you will see that it fails since the route is not defined. Let's add it to our `routes.rb` file:

!FILENAME config/routes.rb
```ruby
Myapp::Application.routes.draw do
  root to: "posts#index"
end
```
Now implement the controller that will serve the /posts route.

!FILENAME app/controllers/posts_controller.rb
```
class PostsController < ApplicationController
  def index
    @posts = Post.all
  end
end
```
And create the view that will render Posts#index action and list all posts.

!FILENAME app/views/posts/index.html.erb
```erb
<ul>
<% @posts.each do |post| %>
<li>
<%= post.title %><br />
<%= post.content %>
</li>
<% end %>
</ul>
```
Now run the feature file and you should see it pass:
```
$ bundle exec cucumber features/home_page.feature
...

1 scenario (1 passed)
3 steps (3 passed)
```
####Wrap up
You should now be fully equipped to work in the BDD cycle and deliver clean, robust and working code.









