Add `cucumber-rails` and `database_cleaner` gems to the test group of the Gemfile.

    group :development, :test do
      [...]
      gem 'cucumber-rails', require: false
      gem 'database_cleaner'
    end

The `database_cleaner` gem is used to ensure a clean database state for testing and will make your development easier.

Run this command to bootstrap the application with Cucumber:

    $ bundle exec rails generate cucumber:install

This will generate Cucumber configuration files and set up the database for Cucumber tests. The generator will also create all the code you need to integrate [DatabaseCleaner](https://github.com/DatabaseCleaner/database_cleaner#cucumber-example) into your Rails project.

As a last step, you will need to create the database and run the `migrate` command (even if we have not created any tables and columns yet)

    $ rails db:create --all
    $ rails db:migrate

Just as a sanity check, we want to run Cucumber to see if everything is working properly.

    $ bundle exec cucumber
    Using the default profile...
    0 scenarios
    0 steps
    0m0.000s
    Run options: --seed 44264

    # Running:

    Finished in 0.001691s, 0.0000 runs/s, 0.0000 assertions/s.

    0 runs, 0 assertions, 0 failures, 0 errors, 0 skips

You should now be fully equipped to drive your Rails application using the Acceptance-Unit Test Cycle. Next up, we will go through the cycle of developing a feature using this approach.

> **Note:** As in all other projects during the course we will be storing our work using git. Initialize a git repository inside the application's directory and remember _**you should commit early and often**_