This guide assumes that you have already set up devise token auth on your rails API.

Let's start with the request spec.

`$ touch spec/requests/api/v1/user_can_signup_with_facebook_spec.rb`

```rb
# spec/requests/api/v1/user_can_signup_with_facebook_spec.rb

RSpec.describe 'POST /api/v1/auth/sign_in', type: :request do
  let(:headers) { { HTTP_ACCEPT: 'application/json' } }
  
  describe 'successfully' do
    before do
      post '/api/v1/auth',
      params: {
        uid: '1234567890',
        email: 'user@mail.com',
        provider: 'facebook'
      }
    end

    it 'returns auth token' do
      expect(response_json).to include 'auth_token'
    end

    it 'returns client_id' do
      expect(response_json).to include 'client_id'

    end

    it 'returns uid' do
      expect(response_json['uid']).to eq '1234567890'

    end

    it 'returns expiry' do
      expect(response_json).to include 'expiry'
    end

    it 'returns 200 response' do
      expect(response.status).to eq 200
    end
  end
end
```

In this guide, we will only go over the happy path.

This looks very similar to what we do in the registartions_spec.rb. We are making a request to `/api/v1/auth` with some params. In the regular registration we send in an email, password and password confirmation. When we are signing in with the help of Facebook, we will send in some information we get from Facebook. 

So where do we get the information from Facebook? We will get that information from our client. The user will click to log in with Facebook on the client. Then the user will be presented with a Facebook popup where they authorize that our application gets access to their Facebook profile data. When the user accepts this, Facebook will return the users' Facebook profile data to our client and we are going to use that data to create a user in our API. So when we get that data from Facebook, the client will send a request to `/api/v1/auth` with some of the information that we got from Facebook about the user. That information will be the `email` of the user and the `uid`. `uid` is the id that the user has on Facebook.

The params that we send in with the request in this spec file is the information we are going to get from Facebook.

If you run the test now, you can see that we get a bunch of error messages about how there is no `password_confirmation` etc. 

DeviseTokenAuth does not automatically understand what we are trying to do, we are going through the standard flow of creating a user with devise. So we need to override the devise token auth registrations controller and tell it how to handle sign up with facebook. 

Modify the line in `routes.rb` where we mount the devise token auth. We will tell devise here that we will override their registrations controller. 

```rb
mount_devise_token_auth_for 'User', at: 'api/v1/auth', controllers: {
  registrations: :facebook_login
}
```

We will call the controller that overrides their controller for `FacebookLogin`.

Now it is time to create that controller. In the controller folder (might change depending on ur namespaces) you want to create a new file and call it `facebook_login_controller.rb`.

`$ touch app/controllers/facebook_login_controller.rb`

The file should look like this

```rb
class FacebookLoginController < DeviseTokenAuth::RegistrationsController
  
  def create
    if params[:provider] == 'facebook'
      user = User.from_omniauth(params.require(:uid), params.require(:email), params.require(:provider))
      if user.persisted?
        sign_in user
        token = user.create_token
        user.save
        auth_params = {
          auth_token: token.token,
          client_id:  token.client,
          uid:        params[:uid],
          expiry:     token.expiry,
        }
        render json: auth_params
      else
        render json: { error: 'Authentication with Facebook failed' }, status: 401
      end
      return
    else 
      super
    end
  end
end
```

As you can see at the top of this file we are declaring the new controller, but it is also inheriting from the `DeviseTokenAuth::RegistrationsController`. That means that all of the functionality we get from their controller will be available in this controller. You could call it the parent controller. 

The first line in the create action is where we check if this is a Facebook login/signup or if it is "regular" one. If we are not creating a user that has been provided from Facebook we call on command named `super`. What this does is that it will go to the usual path of creating a user wich devise token auth provides us.

If the provider key in the params equals to `facebook`, we will not go through the usual flow. The first thing we do is to call on an instance method from the `User` model called `.from_omniauth`. This is class method will have to be added to the `User` model. Usually when we create an instance of a model we use `User.create`, but when we are creating a user with Facebook the circumstances change a bit. What we are passing into this class method is the `uid`, the unique user id we get from Facebook. We are also passing in the `email` of the user and the `provider` which will equal to `Facebook`. We will add the `from_omniauth` class method after we have gone through what is happening in the `FacebookLoginController`.

If the `user` persisted, we want to create a current session for that user. That is what is happening on the `sign_in user` line. After that, we create a token for that user. This token is what will authorize and connect the user to the current session we started on the previous line of code. After we have created the token we need to save it as well, it does not do that automatically when we create it.

We need to send off the token to the client so when the user on the client tries to do something that requires a request to the backend where it is required that the user is logged in, the client can send off the token in the request headers back to the API. This is done so our API can authorize that request is valid and that the user is logged in. So in the `auth_params` variable, we create an object that has the necessary token information and then we send it back to the client.

Now it is time for us to add the `from_omniauth` class method.

```rb
# app/models/user.rb

class User < ActiveRecord::Base
  extend Devise::Models
  devise :database_authenticatable, :registerable,
          :recoverable, :rememberable, :trackable, :validatable
  include DeviseTokenAuth::Concerns::User

  def self.from_omniauth(uid, email, provider)
    where(uid: uid, provider: provider, email: email).first_or_create do |user|
      user.email = email
      user.password = Devise.friendly_token[0,20]
    end
  end
end
```

Here we either create a user with the information that we get from Facebook or we find a user that already exists in our database that has the same data that we got from Facebook. This means that we will not create duplicate users every time the user wants to authenticate with Facebook on the client-side. If the user does not exist in the APIs database then it creates a new user and sets the email to equal the email we got from Facebook and randomly generates a password. This also means that the user can never really log in using the "regular" authentication flow since they do not know their password. They will always have to authenticate through Facebook.

If you run the tests now everything should go green!
