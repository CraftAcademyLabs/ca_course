Let's think about how we want our Articles to be structured:

*   We need to be able to store Articles in our database - meaning we need to create a Model
*   We want each article to have a `title` and `content` - we need to add these attributes to our Article model
*   We don't want to store articles that don't have `title` and `content` present - meaning we need to add a validation that these attributes are not empty
*   We want to make sure that there is a valid Factory to use in our test environment

The first thing we want to do is to create a spec file in the `spec/models` folder called `article_spec.rb`. Let's add some tests (we will use the matchers provided to us by the `shoulda-matchers` gem that extends the built-in RSpec matchers).

**`spec/models/article_spec.rb`**

    require 'rails_helper'

    RSpec.describe Article, type: :model do
      describe 'DB table' do
        it { is_expected.to have_db_column :id }
        it { is_expected.to have_db_column :title }
        it { is_expected.to have_db_column :content }
      end

      describe 'Validations' do
        it { is_expected.to validate_presence_of :title }
        it { is_expected.to validate_presence_of :content } 
      end

      describe 'Factory' do
        it 'should have valid Factory' do
          expect(FactoryBot.create(:article)).to be_valid 
        end
      end
    end

If you run RSpec (and you should) now you will see the tests fail - we haven't created the model yet.