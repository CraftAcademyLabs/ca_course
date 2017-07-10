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