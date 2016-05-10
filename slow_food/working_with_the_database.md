## Step 5 - Working with the database

It's time to introduce the database connection. As you've already noticed (if you have reviewed the gems in your `Gemfile`) we'll be using Postgres as a database. In order to access our database using DataMapper - an Object Relational Mapper library that will allow us to treat our data as Ruby Objects. 

Simply put, a class whose instances of we want to store in a database can be defined by adding a series of attributes to it. 

Consider this.

```ruby
class User 
  include DataMapper::Resource
  property :id,    Serial
  property :email, Text 
end 
```

Now, you'll have a set of methods you can use when creating an instance of `User`. Most important are these.

```
> my_user = User.new
> my_user.name = "Thomas"
> my_user.save
```

Or, you can do something like this. 

```
> another_user = User.create(name: "Thomas")
```

In your code, there is already a `User` class. Locate it and examine the code closely. 

In order to be able to open an interactive console that will allow you to access tis class definition and create new instances, use this command.

```shell
$ bundle exec irb -r ./lib/controller.rb
```

That will open the IRB console with all your gems and dependencies loaded. 