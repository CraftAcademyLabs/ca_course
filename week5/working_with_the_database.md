## Working with the database

It's time to tsake another look the database connection. As you've already noticed (if you have reviewed the gems in your `Gemfile`) we'll be using Postgres as a database. 

If you haven't installed Postgres yet, it's time to do it now. There are several different ways to install Postgres on your computer and there is a bid risk that you will run into some issues while doing it. The simplest way is to head over to http://postgresapp.com/ and download and run the installer.

The setup is fairly straight forward if you follow instructions. If you run into some issues with Postgres, please ask your coach for help.

 Another way to install Postgres is using Homebrew. Just run the following command:

```shell
$ brew install postgres
```

### ActiveRecord
In order to access our database, we will be using ActiveRecord - an Object Relational Mapper library that will allow us to treat our data as Ruby Objects. 

Simply put, a class whose instances of we want to store in a database can be defined by adding a series of attributes to it. 

Consider this:

```ruby
class User < ActiveRecord::Base
end

# And the the following migration

class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :name
      t.integer :age
    end
  end
end
```
When you run the migration, using `$ rails db:migrate`, the table `users`with columns `name` and `age` your database will be added to your database. 

Now, you'll have a set of methods you can use when creating an instance of `User`. Most important are the following:

```
> my_user = User.new
> my_user.name = "Thomas"
> my_user.age = 46
> my_user.save
```

Or, you can do something like this. 

```
> another_user = User.create(name: "Oliver", age: 21)
```

In order to be able to open an interactive console that will allow you to access tis class definition and create new instances, use this command.

```shell
$ rails c
```

That will open the Rails console with all your gems and dependencies loaded. Now, you can try to create a new user using the class definition in the legacy code. 

```shell
Loading development environment (Rails 5.2.1)
[1] pry(main)> User.count
 => 0 
[2] pry(main)>  my_user = User.new
 => #<User id: nil username: nil age: nil> 
[3] pry(main)>  my_user.username = "Tochman"
 => "Tochman" 
[4] pry(main)> my_user.age = 46
 => 46 
[5] pry(main)> my_user.save
 => true 
[6] pry(main)> my_user
 => #<User id: 2 username: "Tochman" age: "46"> 
[6] pry(main)> quit
```








