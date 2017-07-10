## Sinatra - Modular Applications
So far we've been using a very basic Sinatra setup and written no tests. Lets bump up the complexity a bit and introduce a more advanced setup and Acceptance Tests

In your terminal create a new project folder:
```
mkdir my_app
```
cd into that folder and run the following command to install `rspec-sinatra` - a gem that will help us scaffold a Modular Sinatra application. This gem is created by a good friend of Craft Academy, Professor Sam Joseph of [Agile Ventures](https://www.agileventures.org/). 

** Note that you need to be on Ruby 2.3.3 in order for this gem to work. Please see [this issue for more in depth discussion](https://github.com/tansaku/rspec-sinatra/issues/2). **

```shell
$ gem install rspec-sinatra
```


In order to create an application, run this command in your terminal.  
```shell
$ rspec-sinatra init --app  MyApp lib/my_app.rb
```
Open the code in your editor and read through the scaffold - it differs a bit from the setup we used in previous chapters. 



Also, edit the `.rspec` file and add `--format documentation` to see a more verbose rspec output. Your `.rspec` file needs to look like this:

```
--format documentation
--color
--require spec_helper
```


Create a Gemfile: `touch Gemfile` and add some basic libraries you will be using: 
```
source 'https://rubygems.org'

gem 'sinatra'

group :development, :test do
  gem 'capybara'
  gem 'launchy'
  gem 'rspec'
  gem 'shotgun'
  gem 'rack-test'
end
```
Run `bundle install` in your terminal

Also add `config.include Rack::Test::Methods` to your `Rspec.configure block` in `spec/spec-helper.rb`.

If you run RSpec now you should see the following output: 
```shell
$ rspec
No examples found.

Finished in 0.00034 seconds (files took 1.6 seconds to load)
0 examples, 0 failures
```


### Working with views
We will introduce the concept of a application Layout File that can be used to wrap around all the other views in our application. The keywor here id the `yield` command. Lets show by an axample. 

Create a `features` folder inside the `spec` folder.
```shell
$ mkdir spec/features
```

First we start to write our test. Create a `my_app_spec.rb` in the `features` folder. Add your first test:
```ruby
describe 'home page' do

  it "displays text" do
    visit '/'
    expect(page.current_path).to eq '/'
    expect(page).to have_content 'This is in my index file'
  end
  
  it "displays layout text" do
    visit '/'
    expect(page).to have_content 'This is in my application layout file'
  end
  
end

```
Read through the code carefully. What we are doing here is that we tell RSpec to:
1. Visit the Root Path. 
2. Test if we actualy ARE on the Root Path.
3. Test for some content on the page.

So we have a test but we have no implementation code. 

We need to modify the route in the `my_app.rb` file (that is the file that, among other things, tells the application what to do depending on the request it gets).

Change the `get '/'` route to render the `index.erb` template:

```ruby
# lib/my_app.rb

  get '/' do
    erb :index
  end
```


In order to get the assets to load, modify your `config.ru` to include:
```
use Rack::Static, urls: ['/css', '/js', '/images', '/fonts'], root: 'assets'
```
And create the folders. 
```shell
$ mkdir assets
$ mkdir assets/css
$ mkdir assets/js
$ mkdir assets/images
$ mkdir assets/fonts
```

Also, you are going to need a template file in your `views` folder. We need to create that folder and template file:

```shell
$ mkdir lib/views
$ touch lib/views/index.erb

```


Add following content to `index.erb`:

```erb
<p>This is in my index file</p>
```


We still need to create a Layout file to make the second spec pass. 
```shell
$ touch lib/views/layout.erb

```

Open that file and add:

```erb
<h1>This is in my application layout file<h1>
<%= yield %>
```

Running RSpec now should make the tests go green. 

```shell
$ rspec

home page
  displays text
  displays layout text

Finished in 0.03739 seconds (files took 0.75569 seconds to load)
2 examples, 0 failures

```

### Basic styling

So we are good on our specs. But lets do a bit more.

Create a file called `app.css` in the `assets/css` folder. Add this code to that file

```css
h1 {
  color: red;
}
```

Modify your `views/layout.erb` to better follow the HTML standards and make sure we actually load the css. 
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>My First Sinatra App</title>
    <link rel="stylesheet" href="/css/app.css">
  </head>
  <body>
    <h1>This is in my application layout file<h1>
    <%= yield %>
  </body>
</html>

```


### Reflect and review
1. Google each and every one of the gems in the `:development, :test` group of your Gemfile. Try the following searches:
```
ruby gem capybara
ruby launchy gem
etc...
```
What are these gems and what do they do? Can you see why we have included them? Write a short description with an explanation of each gem and add it to this small projects README. 

2. What can you say about the minimum requirements of a HTML-5 page? What needs to be there and why?

3. When we speak about `assets`, what do we mean?




