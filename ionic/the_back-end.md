# The Back-end
We will be using Ruby on Rails as the stack for our back-end.

The challenge is to set up an API-only application that will make it possible to store information about users and their historical data. 

We will be using RSpec as out testing framework and PostgreSQL as out database. 

Before moving on, make sure you scaffold an new Rails application. If you need assistance you can check out the [BDD with Rails](https://craftacademy.gitbooks.io/coding-as-a-craft/content/bdd_with_rails.html) chapter. 

There are some steps that we need to undertake to prepare the application before we can start adding our api-endpoints. 

First, we need to create a custom `ApiController` that we will use to inherit from. 

```
$ touch app/controllers/api_controller.rb
```
!FILENAME app/controllers/api_controller.rb
```ruby
class ApiController < ActionController::Base
  protect_from_forgery with: :null_session
  skip_before_filter :verify_authenticity_token
end
```
We use `protect_from_forgery with: :null_session` to avoid running into an `ActionController::InvalidAuthenticityToken` exception. 

When you make a request from some other client, like a Angular app accessing a REST service, you will get errors by default, since you do not have the secret token. Rails allows you to alter the behavior when the secret isn't sent in your request.  By default it throws an exception, but you can change it to just set the session to NULL

We also need to add `skip_before_filter :verify_authenticity_token` because Rails will return 422 status code and error message ‘Can’t verify CSRF token authenticity’ (**TODO: move to a section AFTER we have added Devise?**) 

We also want to add the [`rack-cors`](https://github.com/cyu/rack-cors) gem to allow external clients to access our application. Add the dependency to your Gemfile:

!FILENAME Gemfile
```ruby
gem 'rack-cors', :require => 'rack/cors'
```

Put something like the code below in `config/application.rb` of your Rails application. For example, this will allow GET, POST, PUT and DELETE requests from any origin on any resource.

!FILENAME config/application.rb
```ruby
module YourApp
  class Application < Rails::Application
    # [...]
    config.middleware.insert_before 0, "Rack::Cors" do
      allow do
        origins '*'
        resource '*', headers: :any, methods: [:get, :put, :delete, :post]
      end
    end
  end
end
```
There are plenty of settings you can add to enchance security of your application. read about it in the `rack-cors` gem documentation.

###Testing with RSpec
We will be using request specs to test our api endpoints. 
Let's create a dummy endpoint just to make sure everything is okay in terms of security settings. 

In your `routes.rb` create an API namespece and add V0 within it. 

!FILENAME config/routes.rb
```ruby 
# [...]
namespace :api do
  namespace :v0 do
    resources :ping, only: [:index], constraints: {format: /(json)/}
  end
end
```

In the `app/controllers` folder, create the following folder structure.
```
$ mkdir app/controllers/api
$ mkdir app/controllers/api/v0
```

Inside that folder, we want to create the 
 




