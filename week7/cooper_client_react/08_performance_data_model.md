Let's add a way to store historical data for each user.

Run the following generator.

`rails g model PerformanceData user:references data:hstore --force-plural`

Note: What does the `--force-plural` flag do for us?

Also, please note that we are using a datatype that we've not used before, the `hstore`. That is not a standard datatype in certain versions of PostgreSQL, so we need to make sure it will work. Open up the migration and add a line that adds `hstore` as a datatype and enables the database to store hashes.
```
# db/migrate/XXXX_create_performance_data.rb
class CreatePerformanceData < ActiveRecord::Migration[6.0]
  def change
    execute 'CREATE EXTENSION IF NOT EXISTS hstore'
    create_table :performance_data do |t|
      t.references :user, index: true, foreign_key: true
      t.hstore :data

      t.timestamps null: false
    end
  end
end
```

In your User model, add the following relationship.
```
# app/models/user.rb
class User < ActiveRecord::Base
  # [...]
  has_many :performance_data, class_name: 'PerformanceData'
end
```

Run the new migration.

`$ rails db:migrate`

Add the following specs.
```
# spec/models/user_spec.rb
RSpec.describe User, type: :model do
  # [...]

  describe 'Relations' do
    it { is_expected.to have_many :performance_data }
  end
end
```

And to the newly created `spec/models/performance_data_spec.rb`.
```
# spec/models/performance_data_spec.rb
require 'rails_helper'

RSpec.describe PerformanceData, type: :model do
  describe 'Database table' do
    it { is_expected.to have_db_column :data }
  end

  describe 'Relations' do
    it { is_expected.to belong_to :user }
  end
  
  describe 'Factory' do
    it 'should have valid Factory' do
      expect(create(:performance_data)).to be_valid
    end
  end
end
```

Make sure that the factory for performance data looks like this:
```
# spec/factories/performance_data.rb

FactoryBot.define do
  factory :performance_data, class: 'PerformanceData' do
    user
    data { "" }
  end
end
