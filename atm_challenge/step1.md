# Step 1 - Setting the stage

The first thing we need to do is to set up the necessary tools we'll be using. We know that we'll be using Ruby as the programming language. That is already set up on our system. 

We also know that we'll be trying to write our application using Test Driven Development - or at least try to do that. For that we'll need a testing framework. Enter RSpec - the most frequently used testing library for Ruby applications. Even though it has a very rich and powerful DSL (domain-specific language), at its core it is a simple tool which you can start using rather quickly. 

In order to be able to use it we need to install it. There are two ways to install libraries (gems). A direct install from your terminal or by adding a gem as a dependancy to your application using [Bundler](http://bundler.io/). It is pretty simple, you just add a gem to a specific file named `Gemfile`. 

Let's do that. 

Create a new `Gemfile` from your terminal in the folder that you want to use for your application.

```
$ touch Gemfile
```
Add the following content to that file. 

!FILENAME Gemfile
```ruby
source 'https://rubygems.org'

gem 'rspec'
```

Save and head over to your terminal window and run the `bundle install` command.


