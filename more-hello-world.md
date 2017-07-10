### Dynamic content

Let's see if we can make this a bit more interesting.

Modify your `hello_path` to look like this:

```ruby
get '/hello/:name' do
  "<h1>Hello #{params[:name]}</h1>"
end
```

Params is a hash that Sinatra makes available for you in your route blocks, and it will automatically include relevant data from the request.
In our case our route specifies a path that is a pattern: the last part of the path starts with a colon `:`. This tells Sinatra that we’d like to accept any string here, and that we’d like to call this string `name`.

