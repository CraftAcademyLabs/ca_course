## Testing the endpoints - request specs

Moving on with our specs. The Devise Token Auth gem gave us a lot of functionality. We can register an account, edit it and also delete the account altogether. We can also, of course, authenticate a user.

We will focus on 2 parts of the application: Account Registration and User Authentication.

First we need to add a helper method that will parse the server response body to a JSON object in order to be able to assert that the response is matching our expectations (the server transforms any JSON object into JSON text and stores that JSON text in a string, therefore, we need to parse that strung into a JSON again in our tests).

To avoid code repetition and DRY out our request specs, we want to create a custom helper. Start by creating a `support` folder in the `spec` folder. Add a new file named `response_json.rb`. In that file we will create a module that will parse the body of the server response (`response.body`) back to JSON.

```ruby
# spec/support/response_json.rb
module ResponseJSON
  def response_json
    JSON.parse(response.body)
  end
end

RSpec.configure do |config|
  config.include ResponseJSON
end
```