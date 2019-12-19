Let's create the controller we will use to create, update and retrieve user's historical data.

This is where we will be performing our CRUD actions.

The first thing we want to be able to do is to save data.

Let's create a request spec and start adding functionality to our controller.

In the `spec/requests/api/v1/` folder we want to create a new test file. Let's call it `performance_data_spec.rb`. We can start with a simple test to see if our entry will be saved to the database (it WILL fail at first, but that is the way we do it, right?)
```
# spec/requests/api/v1/performance_data_spec.rb
RSpec.describe Api::V1::PerformanceDataController, type: :request do
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  describe 'POST /api/v1/performance_data' do
    before do
      post '/api/v1/performance_data', 
      params: {
        performance_data: { 
          data: { 
            message: 'Average' 
          } 
        }
      }, 
      headers: headers
    end

    it 'successfully creates a data entry' do
      entry = PerformanceData.last
      expect(entry.data).to eq 'message' => 'Average'
    end

    it 'returns a 200 response status' do
      expect(response.status).to eq 200
    end

    it 'returns a success message' do
      expect(response_json['message']).to eq 'all good'
    end
  end
end
```

Create a new file in the following path: `app/controllers/api/v1/` Call it `performance_data_controller.rb` and add the class definition to it.
```
# app/controllers/api/v1/performance_data_controller.rb
class Api::V1::PerformanceDataController < ApplicationController

end
```

We also need to create a route for that so let's modify our `routes.rb` by adding a post route to it. Update your `config/routes.rb` with the following code. Note the addition of `defaults: { format: :json }` to our `v1` namespace. This will constraint the application to respond to json requests only.
```
# config/routes.rb
namespace :v1, defaults: { format: :json } do
  # [...]
  resources :performance_data, only: [:create]
end
```

In your terminal, run `$ rails routes` to make sure everything is working. You should have a new route pointing to the `performance_data_controller.rb`'s `create` method.
```
api_v1_performance_data POST /api/v1/performance_data(.:format) api/v1/performance_data#create {:format=>:json}
```

So far so good...

Next, let's add a `create` method with the following code to our new controller in order to get the test to pass.
```
# app/controllers/api/v1/performance_data_controller.rb
class Api::V1::PerformanceDataController < ApplicationController
  def create
    data = PerformanceData.new(params[:performance_data])
    if data.save
      render json: { message: 'all good' }
    end
  end
end
```

When you run the test now you will get an error with Rails complaining about `ForbiddenAttributesError`
```
Performance Data
  POST /api/v1/performance_data/
    creates a data entry (FAILED - 1)

  Failures:

    1) Performance Data POST /api/v1/performance_data/ creates a data entry
        Failure/Error: @data = PerformanceData.new(params[:performance_data])

        ActiveModel::ForbiddenAttributesError:
          ActiveModel::ForbiddenAttributesError
        # ./app/controllers/api/v1/performance_data_controller.rb:4:in `create'
        ....
```

That is due to our params not being whitelisted. Some explanation is in place.

Action Controller parameters are forbidden to be used in Active Model mass assignments until they have been whitelisted. Strong Parameters provides an interface for protecting attributes from end-user assignment.

So we need to whitelist our params. There are many different approaches for doing that. For now, we will whitelist all params contained on the `:performance_data` key and update the `create` method to use them.

Also, go through [this resource about Action Controller](https://edgeguides.rubyonrails.org/action_controller_overview.html#parameters) to fully understand what is going on in the controllers we create and use in our application.

Modify the `performance_data_controller.rb` with this code:
```
# app/controllers/api/v1/performance_data_controller.rb
class Api::V1::PerformanceDataController < ApplicationController
  def create
    data = PerformanceData.new(performance_data_params)

    if data.save
      render json: { message: 'all good' }
    else
      render json: { error: data.errors.full_messages }
    end
  end

  private

  def performance_data_params
    params.require(:performance_data).permit!
  end
end
```

Note: We can use the `permit!` method for now, but there might be some security issues involved with that. Can you figure out a better way?

Okay, at this stage if you run your tests again you'll get a different error

`undefined method "data" for nil:NilClass`

The problem is that we are not assigning a `user` to the created entry, thus resulting in the entry not being created in the DB. The application does not know what user it should associate to the new object.

We need to update our controller to retrieve that information. We can do that with the built-in Devise method `authenticate_user!`. We also need to add the user information to the `performance_data_params`. This can be done with a `merge!` command. Review the following code before you implement it in your `performance_data_controller.rb`.
```
# app/controllers/api/v1/performance_data_controller.rb
class Api::V1::PerformanceDataController < ApplicationController
  before_action :authenticate_api_v1_user!

  def create
    data = PerformanceData.new(performance_data_params.merge(user: current_api_v1_user))

    if data.save
      render json: { message: 'all good' }
    else
      render json: { error: data.errors.full_messages }
    end
  end

  private

  def performance_data_params
    params.require(:performance_data).permit!
  end
end
```
We also need to update our spec and create a user (using Factory Bot) and sent that users credentials with the request (in headers). We will use the DeviseTokenAuth method `create_new_auth_token` to generate the necessary credentials. Make sure to use your debugger and break the test at some point to run this command on the user object manually to see it in action.

Anyway, your spec should look something like this.
```
# spec/requests/api/v1/performance_data_spec.rb
RSpec.describe Api::V1::PerformanceDataController, type: :request do
  let(:user) { FactoryBot.create(:user) }
  let(:credentials) { user.create_new_auth_token }
  let(:headers) { { HTTP_ACCEPT: 'application/json' }.merge!(credentials) }

  describe 'POST /api/v1/performance_data' do
    before do
      post '/api/v1/performance_data', 
      params: {
        performance_data: { 
          data: { 
            message: 'Average' 
          } 
        }
      }, 
      headers: headers
    end

    it 'successfully creates a data entry' do
      entry = PerformanceData.last
      expect(entry.data).to eq 'message' => 'Average'
    end

    it 'returns a 200 response status' do
      expect(response.status).to eq 200
    end

    it 'returns a success message' do
      expect(response_json['message']).to eq 'all good'
    end
  end
end
```

If you run your specs now, you should not be getting any errors as the `PerformanceData` entry is connected to a `User` and no validation errors should be present.

You need, however, to add some tests that hit the sad path and makes sure you have full control of what kind of error messages are being returned if the object you are trying to create fails validation.

We are almost done now with our basic CRUD actions. Let us move on and create a method that will retrieve a collection of PerformanceData objects, but only for the user that makes the request. Let's start by setting the stage for a request spec and test if we get the right response.
```
# spec/requests/api/v1/performance_data_spec.rb
RSpec.describe Api::V1::PerformanceDataController, type: :request do
# [...]
  describe 'GET /api/v1/performance_data' do
    before do
      5.times { 
        create(
          :performance_data,
          data: { 
            message: 'Average' 
          },
          user: user
        ) 
      }
      
      get '/api/v1/performance_data', headers: headers
    end
  
    it 'returns a collection of performance data' do
      expect(response_json['entries'].count).to eq 5
    end
  
    it 'returns a 200 response status' do
      expect(response.status).to eq 200
    end
  end
end
```

Go ahead and run this spec just to get a friendly reminder that there is no such route as `GET /api/v1/performance_data/`. Let's add that to our `routes.rb`
```
# config/routes.rb
namespace :v1 do
  #[...]
  resources :performance_data, only: [:create, :index]  
end
```

The next error you will see if you run the spec now, tells you that there is no `index` method defined in the controller. Let's create that.
```
# app/controllers/api/v1/performance_data_controller.rb
class Api::V1::PerformanceDataController < ApplicationController
  # [...]

  def index
    collection = current_api_v1_user.performance_data
    render json: { entries: collection }
  end

  # [...]
end
```

We would need to add a few more specs to make sure that the `index` method works the way it is intended - to only return `PerformanceData` that belongs to the current user. But I'll leave that for you to add.

Note that we are NOT building any views or templates to display data. We MIGHT need more control of how the json response looks like and for that we will use the template capabilities of `JBuilder`, or use a serializer. But for now, returning a rather uncomplicated JSON object will do.

**That's it! We don't need to create the `update` and `delete` actions. There could be a use-case for them in the future, but at this stage they are not needed.**
