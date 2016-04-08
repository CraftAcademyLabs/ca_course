# Step 1 - Setting the stage

The first thing we need to do is to set up the necessary tools we'll be using. We know that we'll be using Ruby as the programming language. That is already set up on our system. 

We also know that we'll be trying to write our application using Test Driven Development - or at least try to do that. For that we'll need a testing framework. Enter RSpec - the most frequently used testing library for Ruby applications. Even though it has a very rich and powerful DSL (domain-specific language), at its core it is a simple tool which you can start using rather quickly. 

In order to be able to use it we need to install it. There are two ways to install libraries (gems). A direct install from your terminal (`gem install rspec`) or by adding a gem as a dependancy to your application using [Bundler](http://bundler.io/). It is pretty simple, you just add a gem to a specific file named `Gemfile`. 

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

If you get an error message and the system complains about not finding Bundler, just run this command to install it.

```
$ gem install bundler
```
And run `bundle install` again. 

That installs RSpec. 

The next step is to initialize RSpec and configure it for our needs. 

```
$ rspec --init
```

Edit the .rspec file and add --format documentation to see a more verbose rspec output. Your .rspec file needs to look like this.

!FILENAME .rspec
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

###Using Git
Let me put down some ground rules about version control. Commit often, write good commit messages and push up to your GitHub account. That is the only way for us coaches to see your progress. It does not matter if the code is working. We still want to see it. **Bad code is better then No code!**

At this stage you need to set up a git repository. I suggest that you create a GitHub repository, copy the address and add it as a remote to your local repository (We are about to create one). 

In your terminal, initialize a new git repo with the `init` command.

```
$ git init
Initialized empty Git repository in /your/path/atm/.git/
✔ ~/your/path/atm [master|…2] 
```
Next, you need to create a `.gitignore` file. That file is used to keep information about files we want to EXCLUDE from version control. 

```
$ touch .gitignore

!FILENAME .gitignore
```
Add at this to that file.
```
.DS_Store
```

`.DS_Store`(Desktop Services Store) is a file that stores custom attributes of its containing folder, such as the position of icons or the choice of a background image. We don't want to track those files with git.





