### Forms

So far, all that our little application can do is return some HTML when asked, meaning t can respond to `GET` requests by returning HTML.

What if a user want to talk back to our application, and send some data from the browser? You have seen this a million times, there are those little forms on websites that let you enter data and somehow submit it.

Let us create a simple form, submit it and have out application display something containing the data we submitted. 
Lets start with adding this HTML to our `index.erb`

```html
<form action="/hello" method="post">
  <input type="text" name="name">
  <input type="submit">
</form>
```

And add a POST route to `app.rb` 

```ruby
post '/hello' do
  @message = "Hello #{params[:name]}"
  erb :index
end
```

Okay, so what is happening here? 

The form contains a field with a name attribute called `name` (Pretty confusing, right?). 

This will result in a params key `:name` being created. The method used when submitted is set to `post`. 

That means that the request will use the HTTP verb POST. 


The `@message` instance variable will be set and passed in to the View Template `:index` that will be rendered by the browser. 

### Refactoring exercise
The changes we made will result in some code repetition. During my demo I extracted some of the functionality to a separate method. Can you replicate that? 

There is some errors in the code below. Can you fix that? 

```ruby
require 'sinatra'
require 'sinatra/reloader' if development?
require 'pry-byebug'

get '/' do
  redirect '/hello'
end

get '/hello/:name' do
  format_message(params)
  erb :index
end

post '/hello' do
  format_message(params)
  erb :index
end

def format_message(params)
  if params[:greeting]
    @message = "#{params[:greeting]} #{params[:name]}"
  else
    @message = "Hello #{params[:name]}"
  end
end
```

And the View Template: 

```erb
<h1 style="color: red;"><%= @message %></h1>

<form action="/hello" method="post">
  <input type="text" name="greeting" placeholder ="Greeting">
  <input type="text" name="name", placeholder="Name">
  <input type="submit">
</form>
```



 