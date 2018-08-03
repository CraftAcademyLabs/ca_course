It's time to connect the dots and get the two applications to talk to each other.

First we need to deploy the Rails application to a production server. As usual, we'll be using the simplest solution available.

Let's create a Heroku application

```shell
$ heroku create ca-cooper-api
  # where 'ca' are your initials
```
Now let's create a database and push the app to Heroku.
```shell
$ heroku addons:create heroku-postgresql
$ git push heroku master
$ heroku run rake db:migrate
```
You can manually test the API using `Postman` by doing a POST request to the registration endpoint.

As a matter of fact, you need to create at least one user this way in order to move forward.

![Postman success](/images/cooper_api_postman_sucess.png)

Register a user
And if you try to send the request again, that should fail.
![Registration failure](/images/cooper_api_postman_failure.png)

Alright, if that works we can now shift our focus to the Ionic application, and we'll update it to be able to communicate with the backend.