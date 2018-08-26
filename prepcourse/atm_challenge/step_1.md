# Step 1 - Setting the stage

Let's get back to the ATM challenge. 

## Learning objectives

1. Learn how to set up a simple ruby based project
2. Make use of a testing framework 
3. Connect your project (local repository) to a GitHub repository
4. Practice Pair Programing and sharing code

## Action points

* Set up a simple project   
    1. Create a `Gemfile` and add `rspec` as a dependency.
    2. Initiate `rspec`
    3. Modify `.rspec` to output the spec messages in terminal.
* Create a basic folder structure for your project (production code in `lib`, tests in `spec`)
* Create a `README.md` with a basic description of the project
* Initialize and push a git repository
* Add your pairing partner as a `git remote`

## Let's do it!

Create a project folder in your Terminal and `cd` into that folder

```shell
$ touch atm_challenge
```

As mentioned above, we need to set up the necessary tools we'll be using. We know that we'll be using Ruby as the programming language. That is already set up on our system. 

```
As a programmer
In order to produce good quality code
I want to work in a test driven way and unit test my code
```

We also know that we'll be trying to write our application using Test Driven Development - or at least try to do that. For that, we'll need a testing framework. Enter RSpec - the most frequently used testing library for Ruby applications. Even though it has a very rich and powerful DSL (domain-specific language), at its core it is a simple tool which you can start using rather quickly. 


In order to be able to use it we need to install it. There are two ways to install libraries (gems). A direct install from your terminal (`gem install rspec`) or by adding a gem as a dependency to your application using [Bundler](http://bundler.io/). It is pretty simple, you just add a gem to a specific file named `Gemfile`. 

Let's do that. 

Create a new `Gemfile` from your terminal in the folder that you want to use for your application.

```
$ touch Gemfile
```
Add the following content to that file. 

_Gemfile_

```ruby
source 'https://rubygems.org'

gem 'rspec'
```

Save and head over to your terminal window and run the `bundle install` command.

If you get an error message and the system complains about not finding Bundler, just run this command to install it:

```
$ gem install bundler
```
**Please note that the gem name is `bundler`, while the command used to run it is `bundle`.**

And run `bundle install` again. 

That installs RSpec. 

The next step is to initialize RSpec and configure it for our needs. 

```
$ rspec --init
```

Edit the `.rspec` file and add --format documentation to see a more verbose rspec output. Your `.rspec` file needs to look like this.

_.rspec_

```
--format documentation
--color
--require spec_helper
```

Now, if you go back to your terminal and run the rspec command, you should see something like this.

```
$ rspec
No examples found.

Finished in 0.00028 seconds (files took 0.40297 seconds to load)
0 examples, 0 failures
```

Alright, that means we are set and ready to test. 

### Using Git

Let me put down some ground rules about version control. Commit often, write good commit messages and push up to your GitHub account. That is the only way for us coaches to see your progress. It does not matter if the code is working. We still want to see it. **BAD CODE is better then NO CODE!**

At this stage you need to set up a git repository. I suggest that you create a GitHub repository, copy the address and add it as a remote to your local repository (We are about to create one). 

In your terminal, initialize a new git repository with the `init` command.

```
$ git init
Initialized empty Git repository in /your/path/atm/.git/
~/your/path/atm [master|…2] 
```
Next, you need to create a `.gitignore` file. That file is used to keep information about files we want to EXCLUDE from version control. 

```
$ touch .gitignore
```
Add at this to that file.

_.gitignore_

```
.DS_Store
```

`.DS_Store` (Desktop Services Store) is a OSX file that stores custom attributes of its containing folder, such as the position of icons or the choice of a background image. We don't want to track those files with git.

Now, perform the following steps.

```
$ git remote add origin <your git repo url>
$ git add . 
$ git commit -am "<your message>"
$ git push origin master
```





