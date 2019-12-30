Okay, let's write some specs for user registration.
```
$ mkdir spec/requests/api/v1
## or for mac users
$ mkdir -p spec/requests/api/v1

$ touch spec/requests/api/v1/registrations_spec.rb
```

The examples below contain a lot of moving parts, so be sure to go over the code line by line and discuss with your pairing partner what each line does. We are not using RSpec generators to generate this test (as we did before), because they do not provide a structure of request specs that we want to use. We also introduce a new keyword: `context` that is an alias for `describe` and can be used interchangeably.

Bear in mind the 4 phases of testing:

*   Setup
*   Exercise
*   Verify
*   Teardown

In the first context, the **SETUP** here is the part where we create `:headers`.

The **EXERCISE** is the POST request to `/api/v1/auth`.

The **VERIFY** phase is the assertion we do using the `expect` command.

The **TEARDOWN** is handled by the `use_transactional_fixtures configuration` in `rails_helper`([Read more about transactions](https://relishapp.com/rspec/rspec-rails/v/3-7/docs/transactions)).
```
# spec/requests/api/v1/registrations_spec.rb

RSpec.describe 'POST '/api/v1/auth'', type: :request do
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }

  describe 'with valid credentials' do
    before do
      post '/api/v1/auth',
          params: {
            email: 'example@craftacademy.se',
            password: 'password',
            password_confirmation: 'password'
          },
          headers: headers
    end

    it 'returns a 200 response status' do
      expect(response).to have_http_status 200
    end

    it 'returns a success message' do
      expect(response_json['status']).to eq 'success'
    end
  end

  context 'when user submits' do
    describe 'a non-matching password confirmation' do
      before do
        post '/api/v1/auth',
            params: {
              email: 'example@craftacademy.se',
              password: 'password',
              password_confirmation: 'wrong_password'
            },
            headers: headers
      end

      it 'returns a 422 response status' do
        expect(response).to have_http_status 422
      end

      it 'returns an error message' do
        expect(response_json['errors']['password_confirmation']).to eq ["doesn't match Password"]
      end
    end

    describe 'an invalid email address' do
      before do
        post '/api/v1/auth',
            params: {
              email: 'example@craft',
              password: 'password',
              password_confirmation: 'password'
            },
            headers: headers
      end

      it 'returns a 422 response status' do
        expect(response).to have_http_status 422
      end

      it 'returns an error message' do
        expect(response_json['errors']['email']).to eq ['is not an email']
      end
    end

    describe 'an already registered email' do
      let!(:registered_user) { create(:user, email: 'coach@craftacademy.se') }

      before do
        post '/api/v1/auth',
            params: {
              email: 'coach@craftacademy.se',
              password: 'password',
              password_confirmation: 'password'
            },
            headers: headers
      end

      it 'returns a 422 response status' do
        expect(response).to have_http_status 422
      end

      it 'returns an error message' do
        expect(response_json['errors']['email']).to eq ['has already been taken']
      end
    end
  end
end
```
On line 94 we put a bang (!) after `let`. We need to do this because the variabel that we create on that line will never get referenced anywhere else in the code. Otherwise that user would not be created and we could not properly test that a user cant create an account with an already used email. 

The first spec is the happy path testing that user registration with the minimum of required fields works. The next specs are exposing the error messages we'll get if something goes wrong.

What other possible scenarios, in the context of user registration, should we test for?
