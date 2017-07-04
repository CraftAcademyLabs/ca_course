## Start small - Hello World
We will get started by writing the least amout of code to get a Sinatra application up and running.

We will start with installing Sinatra on our machine. In your terminal, run `gem install sinatra`. That should be rather 
Create a project folder called `dancing_with_sinatra`. 
In that folder, create a file named `app.rb` (yes, Sinatra is a framework based on Ruby, so your Ruby skills will come in handy here.) 

Add the following code to `app.rb` 

```ruby
require 'sinatra'

get '/' do
  'Hello World'
end

```

Now, once you have modified and saved your file, head over to your terminal and run the following command: 

```
$ ruby app.rb

```

That will fire up a web server on your localhost and you will be able to visit `localhost:4567` in your browser to access the application.  

![](/assets/sinatra_hello_world.png)

As you see the string you passed in to the `get '/'` method is displayed on the page. What we've done is:
* We defined a URI (the '/' is often referred to as a `root path`)
* We added some content to be displayed on page that the path will render.

Let's try something else. How about adding some HTML?

Modify your `get '/'` method to look like this: 

```ruby
get '/' do
  '<h1>Hello World</h1>'
end

```

Head over to your terminal, stop the server with command + C and restart it with `ruby app.rb`. Reload the browser window. 

![](/assets/sinatra_hello_world_2.png)

It means that the HTML is being passed in to the view and rendered by the browser, right? That is rather important to know and opens up a lot of possibilities. Lets make use of some Ruby to see if that works. Modify your `get '/'` method to this:

```ruby
get '/' do
  greeting = 'Hello'
  object = 'Earth'
  "<h1>#{greeting} #{object}</h1>"
end
```
Again, stop the server, restart and reload the browser window.

![](/assets/sinatra_hello_world_3.png)

Yeah, so Ruby string interpolation works, right? This means that we can ask Sinatra to execute Ruby code before rendering the view. That is also an valuable insight, right? 






Alright, lets make this more sustainable by adding some settings that will make our development process more sustainable. First of all, the constant restarting of the server needs to stop. Also, we need to keep track of our gems in a better way than installing them directly (what if you need to share the code with another developer for instance? How would she/he know what gems to install?).

In your project folder, create a file called `Gemfile` - if you've worked with Ruby before you recognize that file, right? 

Add the following settings to it:

```ruby
source "https://rubygems.org"

gem 'sinatra'
gem 'sinatra-reloader', '~> 1.0'
```

Save and head over to your terminal. Run this command to install the gems:
```
$ bundle install
``` 
The `sinatra-reloader` gem is an extension to reload modified files. Useful during development, since it will automatically require files defining routes, filters, error handlers and inline templates, with every incoming request, but only if they have been updated.

You have to require that extension in your `app.rb` file to get it to work. 

```ruby
require 'sinatra'
require 'sinatra/reloader' if development?

get '/' do
  greeting = 'Reloaded'
  object = 'Earth'
  "<h1>#{greeting} #{object}</h1>"
end

```

Alright, from now on we can skip the stoping and restarting the server. 







