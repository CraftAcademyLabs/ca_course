## The model
We'll make use of another rails generator to create the Article model. That will create the class, a migration, and a factory (if you are prompted to overwrite the `article_spec`, just type `n`)

`$ rails generate model Article title:string`

So what we did here is tell the **Article** model that is has to have a title and we set the datatype to be a **"string"**.

Following that, you need to run the `rails db:migrate` command to update your database.

```bash
$ rails db:migrate
    == 20160725233007 CreateArticles: migrating ===================================
    -- create_table(:articles)
       -> 0.0295s
    == 20160725233007 CreateArticles: migrated (0.0296s) ==========================
```
Running the spec now you will see that we are moving in the right direction but there are still some failing tests - we have not set up any validations and those assertions are giving us errors.

Open the model file and add validation for the presence of `title`.

```
# app/models/article.rb

class Article < ApplicationRecord
  validates_presence_of :title
end
```

Running your unit specs now (`rspec spec/models/article_spec.rb`) will result in all green tests.