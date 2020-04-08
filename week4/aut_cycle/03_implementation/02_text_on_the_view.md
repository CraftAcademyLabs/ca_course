## Text on the view
Looking at the previous output of RSpec, we note that it expected to see some text on the page, but didn't find any that was specified. It also points us to the file that was supposed to render the text in question `app/views/articles/index.html.erb`.

What we need to do at this stage to get the remaining tests to pass, is add the respective texts to that view file.

Edit `app/views/articles/index.html.erb` and replace the file's content with:

```html
<p>A breaking news item</p>
<p>Some really breaking action</p>
```

Run `RSpec` now and see all the tests pass.

Great! All our steps are now green. We are not quite done yet though. If we take a look at our user story:

```
As a visitor, 
when I visit the application's landing page,
I would like to see a list of articles
```

We want to list several articles on our landing page. All we managed to do so far is display two static texts on our page. Let's update our RSpec feature test to reflect that:

```rb
# spec/features/user_can_see_list_of_articles_spec.rb
require "rails_helper"

feature 'List articles on index page' do
  context 'with articles in db' do
    before do
      create(:article, title: 'A breaking news item')
      create(:article, title: 'Learn Rails 5')

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

We now have a `before` block that will set the stage for our scenario by creating two articles in our database. We then check to see that both articles are displayed on our landing page. If we run RSpec now, we get an error about how `article` is not a registred factory.

```
KeyError:
       Factory not registered: "article"
```

**This is a very crucial step in our development process.** This is as far as you can go in the high-level tests - at this point, we need to shift our attention to the process of creating our units (Article) using tests as a blueprint. It's time to focus on the **lower-level** (inside) tests using RSpec.