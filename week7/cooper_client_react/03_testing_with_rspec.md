**We will be using request specs to test our API endpoints. Request Specs are for API's, what Acceptance Tests/Feature Specs are for web applications. You make a request (get, post, patch or delete) and test if the application responds with the appropriate JSON object (or, in rare cases nowadays, an XML object). JSON is short for JavaScript Object Notation and is a way to store information in an organized, easy-to-access manner. In a nutshell, it gives us a human-readable collection of data that we can access in a logical manner.**

This is the first time you will be working with Request Specs, so let's start slow.

We'll start by creating a dummy endpoint just to make sure everything is okay in terms of security settings.

Let's write our first spec to see if we can get a response from our endpoint.

In your spec folder create a folder named requests. Within that folder, we need to add a folder structure that corresponds to the one we have in our app/controllers folder.
```
$ mkdir spec/requests/api/v0
```

Create a `pings_spec.rb` file and add the following code.
`$ touch spec/requests/api/v0/pings_spec.rb`
```
# spec/requests/api/v0/pings_spec.rb
require 'rails_helper'

RSpec.describe Api::V0::PingsController, type: :request do
  describe 'GET /v0/pings' do
    before do
      get '/api/v0/pings'
    end

    it 'should return a 200 response' do
      expect(response).to have_http_status 200
    end
    
    it 'should return Pong' do
      json_response = JSON.parse(response.body)

      expect(json_response['message']).to eq 'Pong'
    end
  end
end
```

At this stage, if we run this test, it will fail. Let's work on getting that to pass.

Note that if this is the first time you're running the test or the application, rails will throw you an error about non-existent database. Now would be a good time to set them up. Run `rails db:setup --all` then rails `db:migrate` to take care of this.

What we need to do next is create our `PingsController`.

In the `app/controllers` folder, create the following folder structure.
```
$ mkdir app/controllers/api
$ mkdir app/controllers/api/v0
```

**Note: The actual API routes will be placed in another namespace that we will call `V1`. Adding namespaces to your application helps you organize your routes and controllers. Please check out the documentation about [Rails Routing](https://guides.rubyonrails.org/routing.html#controller-namespaces-and-routing) to find out more.**

Inside that folder, we want to create our dummy controller.
`$ touch app/controllers/api/v0/pings_controller.rb`

We will let it inherit from our modified `ApplicationController`
```
# app/controllers/api/v0/pings_controller.rb
class Api::V0::PingsController < ApplicationController

end
```

If we run the test now, the error about undefined constant `Api::V0::PingsController` is gone and RSpec should now throw you a `No route matches [GET] "/api/v0/pings"` error.

In your `routes.rb` create an API namespace and add V0 to it. Nested in that namespace we want to add a `:pings` resource with one single `:index` action.
```
config/routes.rb
Rails.application.routes.draw do
  namespace :api do
    namespace :v0 do
      resources :pings, only: [:index], constraints: { format: 'json' }
    end
  end
end
```

Run `rails routes` in your terminal to see if the route has been added properly.

With our route in place, the test now throws the following error:
```
AbstractController::ActionNotFound:
  The action 'index' could not be found for Api::V0::PingsController
```
To make this spec to pass, we need to add an `index` method to the `Api::V0::PingsController`. When called, we want that method to respond with a JSON object with a single entry: `{message: 'Pong'}`. To achieve this, we will use the `render` method ant tell Rails to output the object.
```
# app\/controllers\/api\/v0\/pings_controller.rb
class Api::V0::PingsController < ApplicationController
  def index
    render json: { message: 'Pong' }
  end
end
```

Does it work? Fire up your server with `rails s` and visit `http://localhost:3000/api/v0/pings`

**Note: You can keep this route and controller around as you move forward, but you might as well delete it. It will NOT be used in your application. We added it purely as an exercise.**
