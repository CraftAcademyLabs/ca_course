**The following guide assumes you're using both RSpec and Cucumber for testing.**

We'll make use of [Coveralls](https://coveralls.io/) a web service that helps track code coverage.
You can use it for various programming languages and for Ruby, there is a gem we can use.

## Setting up Coveralls

Add the following gem to your `Gemfile` in the `:development` and `:test` group

```ruby
group :development, :test do
  gem 'coveralls', require: false
end
```

Run `bundle install`

Next, we need to add coveralls to our testing suite. Add the following lines at the top of each testing
configuration files

```ruby
# spec/rails_helper.rb for RSpec
# features/support/env.rb for cucumber

require 'coveralls'
Coveralls.wear!('rails')
```

Now whenever we run our tests, a coverage report will be generated.

### Merging multiple test suites results

We need to prevent coveralls from sending data right after running each test.
Instead, we want to wait until coverage has been merged before we send it off.
For that, we'll modify the configuration with the following

instead of
```ruby
Coveralls.wear!('rails')
```

we should use
```ruby
Coveralls.wear_merged!('rails')
```

Then create a custom rake task that will run all your test suites then submit coverage
results to the coveralls website.

Create a new file `lib/tasks/ci.rake`

```ruby
unless Rails.env.production?
  require 'rspec/core/rake_task'
  require 'cucumber/rake/task'
  require 'coveralls/rake/task'

  Coveralls::RakeTask.new

  namespace :ci do
    desc 'Run all tests and generate a merged coverage report'
    task tests: [:spec, :cucumber, 'coveralls:push']
  end
end
```

Now on you can run the following command on CI to execute all your tests suites

```bash
$ bundle exec rake ci:tests
```

or if you're using Rails 5+ you can do

```bash
$ bundle exec rails ci:tests
```

## Setting up Continous integration with Semaphore


* Make sure the coaches has set you as admins on the upstream repositry and that you are apart of the Craft Academy
  organization
* Visit the Semaphore classic, make sure that you are not on Semaphore 2s website. https://semaphoreci.com/
* If you dont already have an account, set one up with Github
* Click button to add new project
* Find the upstream repo you want to add CI to in the list which shows the Craft academy repos
* When they ask you who are supposed to own this project, pick craft academy.
* After you selected the repo you want to use you should be able to set which branch we want CI for, usually the      development branch. The branch u will merge feature branches in to.
* After you have selected the branch and semaphore has analyzed the project, you are presented by some options.
  * You usually dont have to change the `Language` & `Ruby version`. Make sure that `database.yml for` option is      set to `pg`.
  * First off delete both of the jobs, we only want the setup.
  * Edit the setup and put this in there
  ```
  bundle install --deployment --path vendor/bundle
  bundle exec rake db:setup
  bundle exec rake db:test:prepare
  COVERALLS_REPO_TOKEN=your_coveralls_token bundle exec rake ci:tests
  ```
  * Make sure to get the repo token from coveralls project and reålace the placeholder in the code example.
* Now go ahead and build with these settings.
  
Semaphore is now successfully added CI with semaphore to your project.

## Setting up Continous Deployment with Heroku

* You need to have created a Heroku application, we will not go over this in this guide.
* Go to your heroku dashboard and click on ur project. 
* Go to the deploy settings tab and activate github as the applications deployment method
* Underneath that option you should now be able to connect the heroku application to a specific repo. That repo       should be the upstream repo. 
* Now you need to decide which branch you want to be deployed, pick the development branch
* There should be a checkbox to wait for CI to pass before deploying, enable it.
* Now click the button that says "Enable Automatic Deploys"

DONE!