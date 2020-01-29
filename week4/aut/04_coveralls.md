## Setting up Coveralls

Add the following gem to your `Gemfile` in the `:development` and `:test` group

```ruby
group :development,  :test  do
  gem 'coveralls',  require:  false
end
```

Run `bundle install`

Next, we need to add coveralls to our testing suite. Add the following lines at the top of each testing configuration file

```ruby
# spec/rails_helper.rb for RSpec
# features/support/env.rb for cucumber

require  'coveralls'
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

When you run your tests coveralls will create coverage reports in folder named `coverage`. We do not want to push that up to Github. Make sure you add `coverage/` to your `.gitignore`

Then create a custom rake task that will run all your test suites then submit coverage results to coveralls.

Create a new file `lib/tasks/ci.rake`

```ruby
  unless  Rails.env.production?
  require  'rspec/core/rake_task'
  require  'cucumber/rake/task'
  require  'coveralls/rake/task'

  Coveralls::RakeTask.new

  namespace :ci  do
    desc 'Run all tests and generate a merged coverage report'
    task tests:  [:spec,  :cucumber,  'coveralls:push']
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

At this point, you have added the code necessary to make coveralls work.

Now you need to visit the [Coveralls website](https://coveralls.io/) and add your project to use their service.

If you haven't created an account already, please sign up using your Github account.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/14_coveralls-sign-up.png)

Once you are logged in you need to get to the page where you can add new repositories. Go to the sidebar and click on "Add repos"

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/15_coveralls-add%20repos.jpg)

The first thing that you want to do is to click on the sync repos button in the top right corner.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/16_coveralls-sync-repo.png)

Now you can search for the repository that you want to add "Coveralls" to. When you find it you need to enable it.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/17_coveralls-enabled.png)

At this point, we have successfully added coveralls to our project. 

If you need to have the coveralls token for continuous integration (Semaphore), click on the Details button.

If you recently added coveralls to the repository you will be presented by the coveralls `repo_token` straight away. If you don't see it when you press details. Then you have to go to the settings for this project to find the coveralls `repo_token`.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/18_coveralls-repo-token.png)
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/19_coveralls-setting-repo-token.png)

