# Connecting the dots

It's time to connect the dots and get the two applications to talk to eachother. 

First we need to deploy the Rails application to a production server. 

Let's create a Heroku application
```
$ heroku create ca-cooper-api 
  # where 'ca' are your initials
```
To enable features such as static asset serving and logging on Heroku please we need to add the `rails_12factor` gem to your `Gemfile`.

!FILENAME Gemfile
```ruby
# [...]
group :production do
  gem 'rails_12factor'
end
```
Make sure to run `bundle` and commit all your changes (You have been making commits during this walk through, right?).

Now let's create a database and push the app to Heroku.

```
$ heroku addons:create heroku-postgresql
$ git push heroku master
$ heroku run rake db:migrate
```
You can manually test the API using Postman by doing a POST request to the registration endpoint. 

![Register a user](/images/cooper_api_postman_sucess.png)
And if you try to send the request again, that should fail.
![Registration failure](/images/cooper_api_postman_failure.png)










