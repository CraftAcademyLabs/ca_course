## User registration

Okay, let's write some specs for user registration.

```shell
$ mkdir -p spec/requests/api/v1
$ touch spec/requests/api/v1/registrations_spec.rb
```

The examples below contain a lot of moving parts, so be sure to go over the code line by line and discuss with your pairing partner what each line does. We also introduce a new keyword: `context` that is an alias for `describe` and can be used interchangeably.

Bear in mind the 4 phases of testing:

- Setup
- Exercise
- Verify
- Teardown

In the first context, the **SETUP** here is the part where we create `:headers`.

The **EXERCISE** is the POST request to `/api/v1/auth`.

The **VERIFY** phase is the assertion we do using the `expect` command.

The **TEARDOWN** is handled by the `use_transactional_fixtures configuration` in `rails_helper`([Read more about transactions](https://relishapp.com/rspec/rspec-rails/v/3-7/docs/transactions)).

```ruby
# spec/requests/api/v1/registrations_spec.rb
RSpec.describe 'User Registration', type: :request do
	let(:headers) { { HTTP_ACCEPT: 'application/json' } }

	context 'with valid credentials' do
		it 'returns a user and token' do
			post '/api/v1/auth', params: { email: 'example@craftacademy.se', 
																		 password: 'password',
																		 password_confirmation: 'password'
																	}, headers: headers

			expect(response_json['status']).to eq 'success'
			expect(response.status).to eq 200
		end
	end

	context 'returns an error message when user submits' do
		it 'non-matching password confirmation' do
			post '/api/v1/auth', params: { email: 'example@craftacademy.se', 
																		 password: 'password',
																		 password_confirmation: 'wrong_password'
																	}, headers: headers

			expect(response_json['errors']['password_confirmation']).to eq ["doesn't match Password"]
			expect(response.status).to eq 422
		end

		it 'an invalid email address' do
			post '/api/v1/auth', params: { email: 'example@craft', 
																		 password: 'password',
																		 password_confirmation: 'password'
																	}, headers: headers

			expect(response_json['errors']['email']).to eq ['is not an email']
			expect(response.status).to eq 422
		end

		it 'an already registered email' do
			FactoryBot.create(:user, email: 'example@craftacademy.se',
																password: 'password',
																password_confirmation: 'password')

			post '/api/v1/auth', params: { email: 'example@craftacademy.se', 
																		 password: 'password',
																		 password_confirmation: 'password'
																	}, headers: headers

			expect(response_json['errors']['email']).to eq ['has already been taken']
			expect(response.status).to eq 422
		end
	end
end
```

The first spec is the happy path testing that user registration with the minimum of required fields works. The next specs are exposing the error messages we'll get if something goes wrong.

What other possible scenarios, in the context of user registration, should we test for?

