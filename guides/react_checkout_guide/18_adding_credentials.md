## Adding credentials

Grab the "secret key" from your Stripe dashboard.

Do not reveal this key. To use this securely we will add the key to our credentials file. Credentials is an encrypted file in rails. We need the master.key file in our config folder to open this file. The master.key is added to your gitignore by default so that you don\t accidentally push it to version control. 

Use the following steps to modify your credentials. 

Head over to your terminal and run the following command.

```bash
$ EDITOR='code --wait' rails credentials:edit
```
Notice that your terminal has been locked and the credentials file has been decrypted and opened in your code editor. The terminal will be locked until you save and close the credentials file in your code editor. But before we do that we need to add the secret key and publishable key in this file.


```yml
secret_key_base: 50fba2642c2978bcf7536a53606328149f18c7382a070822bf86f0d141095d9ed2c2c472b77577df08c34c61e36f3a27aaba26fb746cfb6a79ce3456edbcb04b
stripe:
  pk_key: your_publishable_key_here
  secret_key: your_secret_key_here
```

Save and close the file and you should be getting the following message in the terminal.

```bash
16:16 $ EDITOR='code --wait' rails credentials:edit
File encrypted and saved.
```

To make use of the stripe credentials we need to add some configurations.

Head over to the `application.rb` file where we turn off the generators etc and add the following configurations.

```rb
    config.generators do |generate|
      generate.helper false
      generate.assets false
      generate.view_specs false
      generate.helper_specs false
      generate.routing_specs false
      generate.controller_specs false
    end
    config.stripe.secret_key = Rails.application.credentials.stripe[:secret_key]
    config.stripe.publishable_key = Rails.application.credentials.stripe[:pk_key]
  end
```

We do not want to make actual network calls to stripe when we are in development mode. For that we will use a gem called `stripe-ruby-mock`. Make sure that you use the version stated below.

Add the following gem to the Gemfile

`gem 'stripe-ruby-mock', '~> 2.5.6', require: 'stripe_mock'`

Then we want to update the `rails_helper` to add the mock functionality

Add `require 'stripe_mock'` to the rails_helper and add the configuration to RSpec

```rb
RSpec.configure do |config|
  config.fixture_path = "#{::Rails.root}/spec/fixtures"
  config.use_transactional_fixtures = true
  config.infer_spec_type_from_file_location!
  config.filter_rails_from_backtrace!
  config.include FactoryBot::Syntax::Methods
  config.include(Shoulda::Matchers::ActiveRecord, type: :model)
  config.before(:each) do
    @stripe_test_helper = StripeMock.create_test_helper
    StripeMock.start
  end
  config.after(:each) do
    StripeMock.stop
  end
end
```

