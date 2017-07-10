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

There are other ways to pass in params. Lets have a look.

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

One of the most common way to pass in params to your app is by using forms. We will be covering that in a while.

### View templates

Okay, so we can render HTML in our route methods. What if the HTML grow complex? We would end up with a lot of HTML tags in our method and that is not very readable, right? We can extract the view into a separate file. We refer to these files as Templates or View templates. 

Create a folder called `views` in your project root. In that folder, create a file named `index.erb`. ERB stands for Embedded Ruby. This means that we can embed and execute Ruby code in those HTML files. 

Add the following code to it:
```erb
<h1 style="color: red;"><%= @message %></h1>
```
(I added the red styling so you will see that this is the file actually rendered)

Modify your `hello_path` to look like this:
```ruby
get '/hello/:name' do
  if params[:greeting]
    @message = "#{params[:greeting]} #{params[:name]}"
  else
    @message = "Hello #{params[:name]}"
  end
  erb :index
end

```

Okay, so we are storing the message in a variable and passing it in to the template. The HTML rendered in your browser come from another file that is solely responsible for the page content. 


