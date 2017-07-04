## Start small - Hello World
We will get started by writing the least amout of code to get a Sinatra application up and running.

We will start with installing Sinatra on our machine. In your terminal, run `gem install sinatra`. That should be rather 
Create a project folder called `dancing_with_sinatra`. 
In that folder, create a file named `app.rb` (yes, Sinatra is a framework based on Ruby, so your Ruby skills will come in handy here.) 

Add the following code to `app.rb` 

```ruby
require 'sinatra'

get '/' do
  'Hello World'
end

```

Now, once you have modified and saved your file, head over to your terminal and run the following command: 

```
$ ruby app.rb
```





