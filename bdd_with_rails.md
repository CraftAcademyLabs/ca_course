# BDD with Rails
###Working with BDD

The concept of Behaviour Driven Development (BDD) is pretty simple. You describe what you want the system to do by describing potential users interactions with the different parts of the application. You work outside-in to implement features using the examples to validate that you're building the right thing at the right time. During this workshop you will see BDD in action and will experience, at least partially, the benefits of this method.

The tools we will be using to test our application are RSpec and Cucumber. Those are the two so called testing frameworks that will help us to write good code. During the development process, we'll take an approach that combines high level acceptance tests and low level unit tests to both drive the development process and make sure that we build a robust and well structured application.

###Testing/Development Cycle

A good cycle to follow for BDD is this outside-in approach:

1. Write a high-level (outside) business value example (using Cucumber or RSpec/Capybara) that goes red
2. Write a lower-level (inside) RSpec example for the first step of implementation that goes red
3. Implement the minimum code to pass that lower-level example, see it go green
4. Write the next lower-level RSpec example pushing towards passing #1
5. Repeat steps #3 and #4 until the high-level test (#1) goes green
6. During the process think of your red/green state as a permission status:

When your low-level tests are green, you have permission to write new examples or refactor existing implementation. You must not, in the context of that refactoring, add new functionality/flexibility.

When your low-level tests are red, you have permission to write or change implmentation code only for the purpose of making the existing tests go green. You must resist the urge to write the code to pass your next test, which doesn’t exist, or implement features you’ll need "some day."



####Prerequisites
We assume that you have your Development environment setup according to instructions in this book and that you are working on eaither a Linux or OSX based computer.

###Setting up the application

If you don't have Rails installed beforehand, it is time to install it now. Rails is a ruby gem, so you can install it as any other gem:
```
$ gem install rails
```
Once you are done it is time to scaffold the Rails application we will be working with.

```
$ rails new rails_bdd --database=postgresql --skip-test-unit  --skip-bundle
```
* The `--database=postgresql` selects PostgreSQL as the database (The out-of-the-box setting is MySQL)-
* The `--skip-test-unit` option skips configuring test.unit. In the tutorial, we use RSpec instead.
* The `--skip-bundle` option prevent running bundle install automatically. 

```
$ cd rails_bdd
$ bundle install
```
###Setting up Version Control

As in all other projects during the course we will be storing our work using git. 

Initialize a git repository inside the projects folder.

```
$ git init
```

At this point you should head over to GitHub and create a new repository called `rails_bdd`. Copy the new repositories `https` or `git` address (depending if you have set up your SSH keys on GitHub) and add that as a remote to your local repo. 

```
$ git remote add origin <repo url>
```
Do your first commit and push your code with this sequence of git commands:
```
$ git add .
$ git commit -am "new rails application"
$ git push origin master
```

IF you encounter some problems you might need to `pull` from the remote repo before you make a `push`. This can occur if you initiated a repo with a README file or `.gitignore`

Just do `git pull origin master` and do a `push` afterwards. 

Remember one of the most common pieces of advice you hear during this bootcamp: ***you should commit early and often***. 

###Add RSpec and shoulda-matchers
Add `rspec-rails` gem to the development and test groups of your Gemfile.

[`shoulda-matchers`](http://matchers.shoulda.io/) allows us to test common Rails functionality, like validations and associations, with less code. 

`factory_girl` is a library for setting up Ruby objects as test data. It's essentially a fixtures replacement. It allows you to create objects that are needed in tests without providing a value for each required attribute. If you don't provide a value for a required attribute factory_girl will use the default value that you defined in factory's definition.

```ruby
# Gemfile

group :development, :test do
  gem 'rspec-rails'
  gem 'shoulda-matchers'
  gem 'factory_girl_rails'
end
```
Install the new dependencies with `bundle install`.

Run the RSpec generator to add the testing framework to your rails application
```
$ bundle exec rails generate rspec:install
```
Why `bundle exec` before a command?  `bundle exec` executes a command in the context of your bundle. That means it uses the gems specified in your Gemfile. It basically standardizes the environment under which the program is run. This helps avoid version hell and makes life much easier.

Before you can use `shoulda-matchers`, you need to configure it by choosing the test framework and features of `shoulda-matchers` you want to use. Open `spec/rails_helper.rb` and add the following block.

```ruby 
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails
  end
end
```

Before we move on we need to add another configuration to the Rails application to avoid the generators to scaffold too many files. 

First I want you to make sure that you remove the `coffee-rails` gem from the applications `Gemfile`. Once you've done that, make the following modification to the `config/application.rb` file:

```ruby
# config/application.rb
class Application < Rails::Application
  # Disable generation of helpers, javascripts, css, and view specs
  config.generators do |generate|
    generate.helper false
    generate.assets false
    generate.view_specs false
  end

  # ...
```
One last thing in this setup process, open the `.rspec` file (it is located in your main project folder but it is a hidden file, so your text editor might not show it) and modify it so that the first line is set to:
```
--format documentation
```

Now, in your terminal, type in rspec and hit enter.
The output you see should be something like:
```
No examples found.

Finished in 0.00023 seconds (files took 0.5029 seconds to load)
0 examples, 0 failures
```

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

```ruby
# spec/factories/posts.rb
FactoryGirl.define do
  factory :post do
    title "My first post"
    content "Hello, behavior-driven development world!"
  end
end
``` 
Update the spec to test the db table, the Factory and the validations of post's title and content:
```ruby 
# spec/models/post_spec.rb
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
Update the Post model with validation definitions:

```ruby
# app/models/post.rb
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
Add `cucumber-rails` and `database_cleaner` gems to the test group of the Gemfile:
```ruby
group :test do
  gem 'shoulda-matchers'
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

```gherkin
# features/home_page.feature
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
Let's copy those steps into features/step_definitions/home_page_steps.rb and edit them:
```ruby
# features/step_definitions/home_page_steps.rb
Given(/^there's a post titled "(.*?)" with "(.*?)" content$/) do |title, content|
  @post = FactoryGirl.create(:post, title: title, content: content)
end

When(/^I am on the homepage$/) do
  visit "/"
end

Then(/^I should see the "(.*?)" post$/) do |title|
  @post = Post.find_by_title(title)
  expect(page).to have_content(@post.title)
  expect(page).to have_content(@post.content)
end
```
In these steps we create a post using `factory_girl`, visit the homepage and check if the post is displayed.

If you run the scenario again, you will see that it fails since the route is not defined. Let's add it to our `routes.rb` file:
```ruby
# config/routes.rb
Myapp::Application.routes.draw do
  root to: "posts#index"
end
```
Now implement the controller that will serve the /posts route:
```
# app/controllers/posts_controller.rb
class PostsController < ApplicationController
  def index
    @posts = Post.all
  end
end
```
And create the view that will render Posts#index action and list all posts:
```erb
# app/views/posts/index.html.erb
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
You should now be fully equipped to work in the BDD cycle and deliver clean, working code.












