Let's write some specs for logging in.

`$ touch spec/requests/api/v1/sessions_spec.rb`
```
# spec/requests/api/v1/sessions_spec.rb
# frozen_string_literal: true

RSpec.describe 'POST /api/v1/auth/sign_in', type: :request do
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }
  let(:user) { create(:user) }
  let(:expected_response) do
    {
      'data' => {
        'id' => user.id, 'uid' => user.email, 'email' => user.email,
        'provider' => 'email', 'allow_password_change' => false
      }
    }
  end
  describe 'with valid credentials' do
    before do
      post '/api/v1/auth/sign_in',
          params: {
            email: user.email,
            password: user.password
          },
          headers: headers
    end

    it 'returns 200 response status' do
      expect(response).to have_http_status 200
    end

    it 'returns the expected response' do
      expect(response_json).to eq expected_response
    end
  end

  describe 'with invalid password' do
    before do
      post '/api/v1/auth/sign_in',
          params: {
            email: user.email,
            password: 'wrong_password'
          },
          headers: headers
    end

    it 'returns 401 response status' do
      expect(response).to have_http_status 401
    end

    it 'returns error message' do
      expect(response_json['errors']).to eq ['Invalid login credentials. Please try again.']
    end
  end

  describe 'with invalid email' do
    before do
      post '/api/v1/auth/sign_in',
          params: {
            email: 'wrong@email.com',
            password: user.password
          },
          headers: headers
    end
    
    it 'returns 401 response status' do
      expect(response).to have_http_status 401
    end

    it 'returns error message' do
      expect(response_json['errors']).to eq ['Invalid login credentials. Please try again.']
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
