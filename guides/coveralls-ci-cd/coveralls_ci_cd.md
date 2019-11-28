**The following guide assumes you're using both RSpec and Cucumber for testing.**

We'll make use of [Coveralls](https://coveralls.io/) a web service that helps track code coverage.
You can use it for various programming languages and Ruby, there is a gem we can use.

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

Then create a custom rake task that will run all your test suites then submit coverage results to coveralls.

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

At ths point you have added the code neccessary to make coveralls work.

Now you need to visit the [Coveralls website](https://coveralls.io/) and add your project to use their service. 

If you havent created an account already, please sign up using your Github account.
IMAGE 14

Once you are logged in you need to get to the page where you can add new repos. Go to the sidebar and click on "Add repos"
IMAGE 15

It is important that you are a part of the Craft Academy organization so you can see and add the repositories that is created on the "Craft Academy" account. You also need to check with the coaches that you are added as an admin on that specific repository that you want to add coveralls to, otherwise you will not be able to see it. 

The first thing that you want to do is to click on the sync repos button in the top right corner. 
IMAGE 16

Now you can search for the repository that you want to add "Coveralls" to. When you find it you need to enable it.
IMAGE 17

At this point we have successfully added coveralls to our project. If you need to have the coveralls token for continuous integration (Semaphore), click on the details button.

If you recently added coveralls to the repository you will be presented by the coveralls `repo_token` straight away. 
IMAGE 18

If you dont see it when you press details. Then you have to go to the settings for this project to find the coveralls `repo_token`.
IMAGE 19

## Setting up Continous integration with Semaphore


* Make sure the coaches have set you as admins on the upstream repository and that you are apart of the Craft Academy organization

* Visit the Semaphore classic, make sure that you are not on Semaphore 2s website. https://semaphoreci.com/
![alt text](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/01_semaphore-pick-classic.png "Logo Title Text 1")

 * If you don't already have an account, set one up with Github
* Click the button to add a new project
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/02_semaphore-add-project.png)
* Find the upstream repo you want to add CI to in the list which shows the Craft academy repositories
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/03_semaphore-select-repo.png)
* When they ask you who is supposed to own this project, pick craft academy.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/05_select-owner.png)
* After you selected the repo you want to use you should be able to set which branch we want CI for, usually the development branch. The branch you will merge feature branches in to.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/04_semaphore-select-branch.png)
* After you have selected the branch and semaphore has analyzed the project, you are presented by some options.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/06_semaphore_analyzing-repo.png)
  * You usually don't have to change the `Language` & `Ruby version`. Make sure that `database.yml for` option is set to `pg`.
  * First off delete both of the jobs, we only want the setup.
  * Edit the setup and put this in there
  ```
  bundle install --deployment --path vendor/bundle
  bundle exec rake db:setup
  bundle exec rake db:test:prepare
  COVERALLS_REPO_TOKEN=your_coveralls_token bundle exec rake ci:tests
  ```
  * Make sure to get the repo token from coveralls project and reålace the placeholder in the code example.
  ![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/07_semaphore-project-settings.png)
* Now go ahead and build with these settings.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/08_semaphore-build-with-settings.png)
  
Semaphore is now successfully added CI with semaphore to your project.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/09_semaphore-complete.png)

## Setting up Continous Deployment with Heroku

* You need to have created a Heroku application, we will not go over this in this guide.
* Go to your Heroku dashboard and click on ur project. 
* Go to the deploy settings tab and activate Github as the applications deployment method
* Underneath that option you should now be able to connect the Heroku application to a specific repo. That repo should be the upstream repo. 
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/11_heroku-deployment-method.png)
* Now you need to decide which branch you want to be deployed, pick the development branch
* Now click the button that says "Enable Automatic Deploys"
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/cab620cfb4def367334fa2f95b9e4e8ba25c49e1/guides/coveralls-ci-cd/12_heroku-automatic-deploys.png)
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/cab620cfb4def367334fa2f95b9e4e8ba25c49e1/guides/coveralls-ci-cd/13_heroku-auto-deploy-enabled.png)

DONE!