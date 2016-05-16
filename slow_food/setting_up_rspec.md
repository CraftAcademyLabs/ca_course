## Setting up RSpec & Cucumber

###RSpec

Add the following gems to your `Gemfile`, unless they are already included. 

!FILENAME Gemfile
```ruby
# Gemfile

gem 'data_mapper'
gem 'dm-postgres-adapter'
gem 'pg'
```

Also, we need to start grouping our gems in that `Gemfile`. Please create the following group:

!FILENAME Gemfile
```ruby
group :development, :test do

end
```

Add the following gem to that group:

!FILENAME Gemfile
```ruby
gem 'dm-rspec'
```

The `dm-rspec` gem extends `RSpec` with a set of matchers for DataMapper objects. Having access to those matchers will make our testing much easier.

Also, I would like you to move all gems that we use for testing purposes to the `:development, :test` group.

Your `Gemfile` should include all the gems below:

!FILENAME Gemfile
```ruby
source 'https://rubygems.org'

  gem 'sinatra'
  gem 'padrino', '~> 0.13.0'
  gem 'data_mapper'
  gem 'dm-postgres-adapter'
  gem 'pg'

group :development, :test do
  gem 'cucumber'
  gem 'rspec'
  gem 'capybara'
  gem 'capybara-webkit'
  gem 'dm-rspec'
end
```

Don't forget to do `bundle install` once you have made all the changes.

In terminal run the following command:

```shell
$ rspec --init
```

You should see the following output:

```shell
create   .rspec
create   spec/spec_helper.rb
```

Now, open `.rspec` file (it is located in your main project folder but it is a hidden file, so your text editor might not show it) and modify it so that the first line is set to:

!FILENAME .rspec
```
--format documentation
...
```

Now, in your terminal, type in `rspec` and hit enter.

The output you see should be something like:

```shell
No examples found.

Finished in 0.00023 seconds (files took 0.5029 seconds to load)
0 examples, 0 failures
```

**We are not quite done yet. Sorry. :-(**

I want you to open the `spec/spec_helper.rb` file. You'll see a lot of lines. Most of them are not needed so I would like you to delete all lines that begin with the `#`sign.
These are comments and we don't need them - for now, they are just a distraction.

When you are done you should see something like this:

!FILENAME spec/spec_helper.rb
```ruby
RSpec.configure do |config|
  config.expect_with :rspec do |expectations|
    expectations.include_chain_clauses_in_custom_matcher_descriptions = true
  end

  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end
end
```

Not so bad, ey?

Alright, we need to add some of the libraries/gems we want to use in our `spec_helper`. Modify your file to include the settings below.

!FILENAME spec/spec_helper.rb
```ruby
ENV['RACK_ENV'] = 'test'

require File.join(File.dirname(__FILE__), '..', 'lib/controller.rb')
require 'capybara'
require 'capybara/rspec'
require 'rspec'
require 'dm-rspec'

```

...and in the `RSpec.configure` block, add:

!FILENAME spec/spec_helper.rb
```ruby
...
config.include Capybara::DSL
config.include DataMapper::Matchers
...
```

Now, run `rspec` again and you should receive no errors.

```shell
No examples found.

Finished in 0.00032 seconds (files took 0.72891 seconds to load)
0 examples, 0 failures
```

**Okay, this means that you have successfully added and set up `RSpec` as a testing framework.**

###Cucumber
Make sure that cucumber is added to your `Gemfile`'s `:development, :test` group as a dependency. 

!FILENAME Gemfile
```ruby
group :development, :test do
  gem 'cucumber'
end
```
If it's not, add it and run `bundle install`. Once that is complete I'd like you to initiate Cucumber by simply typing in:
```shell
$ cucumber --init
```
That will create some files and folders for you:
```
create   features
create   features/step_definitions
create   features/support
create   features/support/env.rb
```
Okay, now, in your terminal, you type in:
```
$ cucumber
```
The output you will see should look something like this:
```
0 scenarios
0 steps
0m0.000s
```
Alright, we have installed and initiated the first testing framework. Big step.

