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


