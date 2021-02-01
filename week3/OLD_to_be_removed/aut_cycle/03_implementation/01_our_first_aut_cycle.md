## Our first AUT cycle

Let’s develop a simple feature. In the scenario, a user will visit the application’s landing page and see a list of news articles displayed.

In your `spec` folder create a `feature` folder and in that folder add a `user_can_see_a_of_articles_spec.rb` file.

Let's start by creating a high-level test file.
```rb
# spec/features/user_can_see_list_of_articles_spec.rb

feature 'List articles on index page' do
  context 'with articles in db' do
    before do
      visit root_path
    end

    it 'displays first article title' do
      expect(page).to have_content 'A breaking news item'
    end

    it 'displays second article title' do
      expect(page).to have_content 'Some really breaking action'
    end
  end
end
```

Make sure that you have created a database before you run your test.
`rails db:create db:migrate`

Run the scenario and see it fail.
`$ rspec spec/features/user_can_see_list_of_articles_spec.rb`

You will see that it fails since the route root_path is not defined. Let's add it to our `config/routes.rb` file:

```
Rails.application.routes.draw do
  root controller: :articles, action: :index
end
```

The basic meaning of creating a **route** involves mapping a URL to a controller and an action. When the **Rails** router sees a request it dispatches it to a controller's action matching the URL.

Now implement the controller that will serve the `/` route. For that, we will use a Rails Generator.
```bash
$ rails generate controller Articles index
      create app/controllers/articles_controller.rb
      route get 'articles/index'
      invoke erb
      create app/views/articles
      create app/views/articles/index.html.erb
      invoke rspec
```
So with this command we created a route for our Articles Index, we also created the view or index for our articles to be displayed on.

Now that we have the root_path setup with the ArticlesController, let's run our tests again. We now have a new error.

```
1) List articles on index page with articles in db displays first article title
     Failure/Error: expect(page).to have_content 'A breaking news item'
       expected to find text "A breaking news item" in ""
     # ./spec/features/user_can_see_list_of_articles_spec.rb:12:in `block (4 levels) in <top (required)>'

2) List articles on index page with articles in db displays second article title
    Failure/Error: expect(page).to have_content 'Some really breaking action'
      expected to find text "Some really breaking action" in ""
    # ./spec/features/user_can_see_list_of_articles_spec.rb:17:in `block (4 levels) in <top (required)>'
```