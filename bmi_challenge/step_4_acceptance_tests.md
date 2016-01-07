## Acceptance tests

In order to be able to load the `index.html` as a fixture in Jasmine, we need to have a local web server up and running locally. If we don't we simply can not run tests for the web interface due to CORS conflicts. 

In your main project folder, create a file called `Gemfile` (note that there is no suffix and that the file name starts with a capital 'G') and add the following lines.

```ruby
# Gemfile

gem 'rack'
gem 'sinatra'
```

Also create two other files in the main project folder:
* `config.ru` ** <- Note the suffix!**
* `server.rb`

Add the following code to each of the files:
```ruby
# config.ru

require './server'

run Sinatra::Application

```

```ruby
# server.rb

require 'sinatra'

set :public_folder, proc { File.join(root) }
```

Now, head over to your terminal and start the local web server.
```shell
$ rackup
```
In your browser, type in this URL to access the web page you recently created:

```text
http://localhost:9292/index.html
```
