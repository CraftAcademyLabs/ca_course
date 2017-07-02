# Blank Rails Scaffold with Continuous Integration & Development

It is well outside the scope of this README to explain the benefits of Continuous Integration and Continuous Deployment. If you're intrigued, start [here](http://www.agilemanifesto.org) and keep reading. Otherwise, here we goooooo

## Step-by-step to a scaffolded Rails app with Postgres, Travis and Coveralls:

- **Create repo on GitHub.** 

 On your GH account crate a new public repository and include Rails `.gitignore` and optional `README`. Copy path to repo.
- **Clone to your local machine.** 

  `git clone REPO_PATH` locally where you want your project to live.
- **Create a new Rails app.** 
  
  Use Postgres if you plan to deploy to Heroku. Skip test because we're going to use Cucumber and RSpec instead of Minitest and skip the bundle for now because we're going to add a bunch of gems. `rails new . --database=postgresql --skip-test --skip-bundle` 
  
  Say `n` to overwrite `.gitignore` (and `README`)
- **Setup a branch structure**

  Switch to a development branch so you can keep your `master` branch clean. (We'll populate it later as we accept code into our production copy). `git checkout -b develop`
  
- **Sync with GH**
    
  Stage your new files for version control: `git add .`

  Make your first commit: `git commit -m 'Initial commit'`
  
- **Clean up code**
  
  In `Gemfile`, delete comments and everything in `:development, :test` and add:

  ```ruby
  group :development, :test do
    gem 'rspec-rails'
    gem 'shoulda-matchers'
    gem 'factory_girl_rails'
    gem 'pry'
    gem 'pry-byebug'
    gem 'cucumber-rails', require: false
    gem 'database_cleaner'
    gem 'coveralls', require: false
  end
  ```
 
  Delete the Windows gem at the bottom of `Gemfile`if you're not working in Windows
  
  Remove unnecessary comments from `rails_helper` and `spec_helper`.
  
  In `rails_helper`, inside the `RSpec.configure` block add DatabaseCleaner bits:
  ```ruby  
  config.before(:suite) do
    DatabaseCleaner.strategy = :transaction
    DatabaseCleaner.clean_with(:truncation)
  end
  config.around(:each) do |example|
    DatabaseCleaner.cleaning do
      example.run
    end
  end
```

  Again in `rails_helper`, this time outside the `RSpec.configure` block, add ShouldaMatchers configuration:

  ```ruby
  Shoulda::Matchers.configure do |config|
    config.integrate do |with|
      with.test_framework :rspec
      with.library :rails
    end
  end
  ```

  In `config/application.rb` remove comments and add inside `class Application`:

  ```ruby
  config.generators do |generate|
    generate.helper false
    generate.assets false
    generate.view_specs false
    generate.helper_specs false
    generate.routing_specs false
    generate.controller_specs false
  end
    ```

  Here we're turning off a bunch of auto-generators for things we'll be creating while we build the app. We've turned them all off, but we might want to leave some on if our project is complex or if we are going to have a lot of helpers, etc.


  
- **Install dependencies**

  Run `bundle install`. That installs any missing gems and creates your `Gemfile.lock` to reference them.
  
- **Install and configure testing frameworks**

  **Install Rspec** (for unit testing) with: `bundle exec rails generate rspec:install`

  Configure the terminal output to show test messages rather than green or red dots when RSpec run: In the hidden file`.rspec`, add: `--format documentation`
  
  Run `bundle exec rspec`. This should work now and return no errors (and also no tests, because you haven't written any)
  
  **Install Cucumber** (for acceptance testing) with: `bundle exec rails g cucumber:install`

  Create the test and development databases: `rails db:create --all`
  Migrate the test and development databases: `rails db:migrate --all`
  Run `bundle exec cucumber`. This should not error and find no examples
  
- **(Optional) Create pull request template for Github**:

  - `mkdir .github`
  - `touch .github/PULL_REQUEST_TEMPLATE.md`
  - Add :

    ```
     PT Story: [paste Pivotal Tracker link here]

    Changes proposed in this pull request:

    [item 1 - replace me]
    [item 2 - replace me]

    What I have learned working on this feature: [If you don't put anything here you are doing it wrong!]

    Screenshots: [If you made some visual changes to the application please upload screenshots here, or remove this section]

    ```

## Bump over to Travis to setup Continuous Integration

- Visit [Travis-ci.org](http://www.travis-ci.org):

  - Sign up or whatever you have to do.
  - Hit the little `+` next to `My Repositories`
  - Flip the switch on the repository you just created

- Create a file in the root of your folder called `.travis.yml`. Add:

  ```
  language: ruby
  rvm:
  - 2.4.0
  before_script:
  - bundle exec rake db:create --all
  - bundle exec rake db:migrate
  script:
  - bundle exec rake ci:tests
  services:
  - postgresql
  notifications:
    email: false
  ```

  (Obviously, if you are using a different version of Ruby, you will put that version under `rvm`. And if you want a thousand emails about your builds passing or failing, don't include the last two lines.)

## Now Coveralls for code coverage

- Create `lib/tasks/ci.rake`. We'll create a task to merge all test information and send it to coveralls. Add:

  ```
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

- Create `.simplecov` in root folder to bundle test coverage. Add:

  ```
  SimpleCov.start 'rails'
  ```

- Visit [coveralls.io](http://coveralls.io):

  - Sign up or whatever.
  - Hit the plus sign, then flip the switch on your new repo. (You probably have to refresh to see it.)

- At the very top of `spec_helper`:

  ```
  require 'coveralls'
  Coveralls.wear!
  ```

- In `/features/support/env.rb`: Delete comments. Add:

  ```
  require 'coveralls'
  Coveralls.wear_merged!("rails")
  ```
  These four lines of code will generate code coverage reports for Coveralls. Access them on the Coveralls website.

- Add `/coverage` to `.gitignore`

- `rake` will run both `rspec` and `cucumber` --> should pass with no errors, and no examples

## Continuous Deployment

- If you haven't before, you'll need to install the [Heroku Toolbelt](https://toolbelt.heroku.com/), create an account on Heroku and `heroku login` with those credentials.
- Now `heroku create APP_NAME` twice - once for a development server, once for a production server. (If you fail to specify an app name, Heroku will make one for you).
- You need to obtain your secure Heroku key and encrypt it. You can do so by:

  - `travis encrypt $(heroku auth:token) --add deploy.api_key`
  - Honestly, this has never worked for me. Instead, you an grab your Heroku key with `heroku auth:token` and run: `travis encrypt YOUR_HEROKU_KEY`. If you've never used Travis before, you'll have to `gem install travis`.

- Spin back up that `.travis.yml` file and add:

  ```
  deploy:
    provider: heroku
    api_key:
      secure: YOUR_SECURE_KEY
    app:
      develop: YOUR_DEVELOPMENT_APP
      master: YOUR_PRODUCTION_APP
    on:
      repo: YOUR_GITHUB_REPO
    run:
      - "rails db:migrate"
  ```

  Format your repo like this: `YOUR_GITHUB_NAME/PROJECT_REPO`. This setup will deploy your application after a successful build at develop or master.
  
## Files to Adjust
- `config/database.yml` - rename your databases or they will be stuck at "blank_rails_scaffold".


#### Gem descriptions
Gem | Description
---------------- | :---------------------------------:
Rspec | Unit testing
Shoulda Matchers | Testing matchers for Rspec
Factory Girl | Easy creation of new table entries
Pry | Debugging
Pry-Byebug | Allows you to step through debugger
Cucumber | Acceptance testing
DatabaseCleaner | Wipes the database after tests
Coveralls | Code coverage specs