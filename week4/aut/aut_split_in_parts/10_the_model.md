We'll make use of another rails generator to create the Article model. That will create the class, a migration, and a factory (if you are prompted to overwrite the `article_spec`just type `n`)

    $ rails generate model Article title:string content:text

Following that, you need to run the `migrate` command to update your database.

    $ rails db:migrate
    == 20160725233007 CreateArticles: migrating ===================================
    -- create_table(:articles)
       -> 0.0295s
    == 20160725233007 CreateArticles: migrated (0.0296s) ==========================

Running the spec now you will see that we are moving in the right direction but there are still some failing tests - we have not set up any validations and those assertions are giving us errors.

Open the model file and add validation for the presence of both `title` and `content`.

**`app/models/article.rb`**

    class Article < ApplicationRecord
      validates_presence_of :title, :content
    end

Running your specs now (`rspec`) will result in all green tests.