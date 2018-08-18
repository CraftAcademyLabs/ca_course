## Environment-Specific Credentials for Active Storage

Do you want to use the same storage service for multiple environments (development and production) with Active Storage? Here's a walkthough about how to set up environment-specific encrypted credentials and a small tweak to the Active Storage configuration file.

This approach embraces convention over configuration and allows each environment to use the same storage service, but different credentials for each environment.


## Conventional Scenario, Different Services Per Environment
This is how most folks will probably use Active Storage, using different storage service providers between development and production.

```ruby
# config/storage.yml
local:
  service: Disk
  root: <%= Rails.root.join("storage") %>
amazon:
  service: S3
  access_key_id:
    <%= Rails.application.credentials.aws(:access_key_id) %>
  secret_access_key:
    <%= Rails.application.credentials.aws(:secret_access_key) %>
  region: us-east-1
  bucket: app-bucket

```

```ruby
#config/environments/development.rb
Rails.application.configure do
  config.active_storage.service = :local
end
#config/environments/production.rb
Rails.application.configure do
  config.active_storage.service = :amazon
end
```

You need to set the credentials using Rails encrypted credentials functionality (see guide) 

```
$ rails credentials:edit 
```

In the credentials file, add your access key and secret:
```yaml
aws:
  access_key_id: 12345
  secret_access_key: 67890
```



## Same Service, Different Credentials, Different Environments

### Let’s Try Another Approach
I usually prefer setting up development and test buckets on S3 for my local development to better mirror the staging and production environments locally. Here is how I did this using Active Storage and the new Credentials feature in Rails 5.2.

### Changes to `storage.yml`
The main change to make this happen is to add some ERB goodness to the storage.yml file, making it use the current environment when looking up the storage service information. **Notice that the `dig` call now looks for an environment key as the scope for the actual AWS credentials.**

```ruby
# config/storage.yml
amazon_<%= Rails.env %>:
  service: S3
  access_key_id:
    <%= Rails.application.credentials.dig(
      Rails.env.to_sym, :aws, :access_key_id) %>
  secret_access_key:
    <%= Rails.application.credentials.dig(
      Rails.env.to_sym, :aws, :secret_access_key) %>
  region: us-east-1
  bucket: app-<%= Rails.env %>
```

## Updating the environment configuration
Now we just need to update the environment configs to point to `amazon_development` and `amazon_production` respectively.

```ruby
#config/environments/development.rb

Rails.application.configure do
  config.active_storage.service = :amazon_development
end


#config/environments/production.rb

Rails.application.configure do
  config.active_storage.service = :amazon_production
end
```


## Encrypted credential file changes
Then we just need to update our actual credentials to add the environment key scope to each of the credentials we want to use.

```
$ rails credentials:edit
```

```yml
development:
  aws:
    access_key_id: dev-1234
    secret_access_key: dev-5678

production:
  aws:
    access_key_id: prod-1234
    secret_access_key: prod-5678
```

## Wrap up
These 2 strategies will help you in keeping your attachhments separated between development and production enviroments. 

As the test env goes, you should always set the storage to `:local` 
