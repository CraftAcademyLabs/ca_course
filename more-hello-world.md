### Dynamic content

Let's see if we can make this a bit more interesting.

Modify your `hello_path` to look like this:

```ruby
get '/hello/:name' do
  "<h1>Hello #{params[:name]}</h1>"
end
```
So what's going on here? 

**Params** is a **hash** that Sinatra makes available for you in your route blocks, and it will automatically include relevant data from the request.
In our case our route specifies a path that is a pattern: the last part of the path starts with a colon `:`. This tells Sinatra that we’d like to accept any string here, and that we’d like to call this string `name`.

Sinatra therefore adds the key `name` to the params hash, and sets the given string from the path (i.e. from the URL) to it.

There are ather ways to pass in params. Lets have a look.

```ruby
get '/hello/:name' do
  if params[:greeting]
    "<h1>#{params[:greeting]} #{params[:name]}</h1>"
  else
    "<h1>Hello #{params[:name]}</h1>"
  end
end
```

Try these URL's in your browser: 
```
http://localhost:4567/hello/Thomas
http://localhost:4567/hello/Thomas?greeting=Hi
```

