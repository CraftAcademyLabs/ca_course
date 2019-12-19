Let's write some specs for logging in.

`$ touch spec/requests/api/v1/sessions_spec.rb`
```
# spec/requests/api/v1/sessions_spec.rb
RSpec.describe 'Sessions', type: :request do
  let(:user) { FactoryBot.create(:user) }
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  describe 'POST /api/v1/auth/sign_in' do
    describe 'with valid credentials' do
      before do
        post '/api/v1/auth/sign_in', 
        params: { 
          email: user.email,
          password: user.password
        }, 
        headers: headers

        expected_response = {
          'data' => {
            'id' => user.id, 'uid' => user.email, 'email' => user.email,
            'provider' => 'email', 'name' => nil, 'nickname' => nil,
            'image' => nil, 'allow_password_change' => false
          }    
        }
      end

      it 'returns the expected response' do
        expect(response_json).to eq expected_response
      end

      it 'returns 200 response status'
        expect(response.status).to eq 200
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
      
      it 'returns error message' do
        expect(response_json['errors']).to eq ['Invalid login credentials. Please try again.']
      end
      
      it 'returns 401 response status' do
        expect(response.status).to eq 401
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
      
      it 'returns error message' do
        expect(response_json['errors']).to eq ['Invalid login credentials. Please try again.']
      end
      
      it 'returns 401 response status' do
        expect(response.status).to eq 401
      end
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