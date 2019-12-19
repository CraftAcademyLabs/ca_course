Let's write some specs for logging in.

`$ touch spec/requests/api/v1/sessions_spec.rb`
```
# spec/requests/api/v1/sessions_spec.rb
RSpec.describe 'Sessions', type: :request do
  let(:user) { FactoryBot.create(:user) }
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  describe 'POST /api/v1/auth/sign_in' do
    it 'valid credentials returns user' do
      post '/api/v1/auth/sign_in', params: { email: user.email,
                                              password: user.password
                                          }, headers: headers

      expected_response = {
        'data' => {
          'id' => user.id, 'uid' => user.email, 'email' => user.email,
          'provider' => 'email', 'name' => nil, 'nickname' => nil,
          'image' => nil, 'allow_password_change' => false
        }    
      }

      expect(response_json).to eq expected_response
    end

    it 'invalid password returns error message' do
      post '/api/v1/auth/sign_in', params: { email: user.email,
                                              password: 'wrong_password'
                                          }, headers: headers

      expect(response_json['errors'])
        .to eq ['Invalid login credentials. Please try again.']

      expect(response.status).to eq 401
    end

    it 'invalid email returns error message' do
      post '/api/v1/auth/sign_in', params: { email: 'wrong@email.com',
                                              password: user.password
                                          }, headers: headers

      expect(response_json['errors'])
        .to eq ['Invalid login credentials. Please try again.']

      expect(response.status).to eq 401
    end
  end
end
```

We need to add some settings in the devise token auth initializer to make it work with the client we are going to build after we are done with the Rails API
```
# config/initializers/devise_token_auth.rb

DeviseTokenAuth.setup do |config|
  config.change_headers_on_each_request = false
  config.token_lifespan = 2.weeks
  config.batch_request_buffer_throttle = 5.seconds
end
```