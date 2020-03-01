**The following guide assumes that you have an upstream repository on Craft Academy's Github account, if that is not the case for you then just ignore the parts about adding the "Craft Academy repository" and set your repository there instead. It also assumes that you're using both RSpec and Cucumber for testing.**

In this guide, we will show you how to set up three services that will work together, Semaphore, CD with Heroku and Coveralls.

**Semaphore** is testing your code all the time and keeping software quality high. The premise of CI is to get feedback as early as possible because the earlier you get feedback, the fewer things cost to fix.

In software development, when multiple developers or teams are working on different segments of the same web application, we need to perform an integration test by integrating all modules. To do that an automated process for each piece of code is performed on a daily bases so that all your code gets tested.

**Continuous Deployment with Heroku** is about getting code into production in an automated way. Things should be easy and repeatable. That’s where Continuous Deployment comes into play. The deployment should not be manual. Every time we add some new code to our application it should deploy that new code automatically.

**Coveralls** is a web service to help you track your code coverage over time, and ensure that all your new code is fully covered. You can use it for various programming languages and in this guide, we will use it for Ruby.

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

You must be a part of the Craft Academy organization so you can see and add the repositories that are created on the "Craft Academy" account. You also need to check with the coaches that you are added as an admin on that specific repository that you want to add coveralls to, otherwise, you will not be able to see it.

The first thing that you want to do is to click on the sync repos button in the top right corner.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/16_coveralls-sync-repo.png)

Now you can search for the repository that you want to add "Coveralls" to. When you find it you need to enable it.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/17_coveralls-enabled.png)

At this point, we have successfully added coveralls to our project. If you need to have the coveralls token for continuous integration (Semaphore), click on the Details button.

If you recently added coveralls to the repository you will be presented by the coveralls `repo_token` straight away. If you don't see it when you press details. Then you have to go to the settings for this project to find the coveralls `repo_token`.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/18_coveralls-repo-token.png)
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/19_coveralls-setting-repo-token.png)

## Setting up Continous integration with Semaphore

* Make sure the coaches have set you as admins on the upstream repository and that you are apart of the Craft Academy organization

* Visit the Semaphore classic, make sure that you are not on Semaphore 2s website. https://semaphoreci.com/
![alt text](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/01_semaphore-pick-classic.png  "Logo Title Text 1")

* If you don't already have an account, set one up with Github

* Click the button to add a new project
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/02_semaphore-add-project.png)

* Find the upstream repo you want to add CI to in the list which shows the Craft academy repositories
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/03_semaphore-select-repo.png)

* When they ask you who is supposed to own this project, pick Craft Academy.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/05_select-owner.png)

* After you selected the repo you want to use you should be able to set which branch we want CI for, usually the development branch. The branch you will merge feature branches in to.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/04_semaphore-select-branch.png)

* After you have selected the branch and semaphore has analyzed the project, you are presented by some options.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/06_semaphore_analyzing-repo.png)

### Ruby on Rails appliction

* You usually don't have to change the `Language` & `Ruby version`. Make sure that `database.yml for` option is set to `pg`.

* First off delete both of the jobs, we only want the setup.

* Edit the setup and put this in there

```
bundle install --deployment --path vendor/bundle
bundle exec rake db:setup
bundle exec rake db:test:prepare
COVERALLS_REPO_TOKEN=your_coveralls_token bundle exec rake ci:tests
```
If you are using Rails version 6 or above you should add this:
```
nvm install 13.1.0
nvm use 13.1.0
gem update --system
gem install bundler
bundle install --path vendor/bundle
yarn
bundle exec rails db:setup --all db:migrate
bundle exec rails db:test:prepare
COVERALLS_REPO_TOKEN=your_coveralls_token bundle exec rails ci:tests
```

* Make sure to get the repo token from coveralls project and reålace the placeholder in the code example.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/07_semaphore-project-settings.png) 


### React client

If you are setting up Semaphore to work with a React client you want to go ahead with the `language` and `Node.js version` option that Semaphore picked.

* In the `Setup` job you need to have the `yarn` command.

* In the `Job #1` you will have two commands.
```
./node_modules/.bin/cypress install
yarn run cy:run
``` 

* `cy:run` is a script that you need to add to your `package.json`.
```json
// package.json
  "scripts": {
    // ...
    "cy:run": "BROWSER=none yarn start & cypress run"
  },
```

It should look like this except that your `Node.js version` might be different.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/20_semaphore-react-client-settings.png)

### Finish up Semaphore configuration

* Now go ahead and build with these settings.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/08_semaphore-build-with-settings.png)

You have now successfully added CI with semaphore to your project.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/09_semaphore-complete.png)

## Setting up Continous Deployment with Heroku

* You need to have created a Heroku application, we will not go over this in this guide.

* Go to your Heroku dashboard and click on ur project.

* Go to the deploy settings tab and activate Github as the applications deployment method

* Underneath that option you should now be able to connect the Heroku application to a specific repo. That repo should be the upstream repo.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/11_heroku-deployment-method.png)

* Now you need to decide which branch you want to be deployed, pick the development branch

* Now click the button that says "Enable Automatic Deploys"

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/12_heroku-automatic-deploys.png)

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/13_heroku-auto-deploy-enabled.png)

DONE!
