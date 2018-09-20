---
title: "Keep Your Secrets Safe"
subtitle: "Rails 5 - Encrypted Credentials"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Credentials, Rails"
keywords: [Credentials, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Rails 5 - Encrypted Credentials

Rails release 5.2 \(in RC1 at the time of writing this guide\) handles management of application secrets and credentials in a secure way. The release introduces **encrypted credentials** \(called "secrets" in release 5.1\) that are stored in  `config/credentials.yml.enc` .

## The Master Key

The encrypted `credentials.yml.enc` file will be decrypted in production, using a key stored either in the`RAILS_MASTER_KEY`environment variable, or in a key file. In development environment the application uses `config/master.key` to decrypt the data.

This key file is created for you when you scaffold your new Rails 5.2 app using the `rails new`  command. If it doesn't exist, a key will be created when you run `bin/rails credentials:edit`.

Note that It's safe to check in the encrypted file \(`credentials.yml.enc`\) to VCS but you should never commit the encryption key. This file is \(and should be\) excluded from VCS per default. Check your `.gitignore` to make sure it is there.

You should also note that without the `master.key` file present in your project's `config` folder, you will experience errors. This might happen, for example, when you fork and clone an existing project and try to run `rails db:setup` \(or equivalent\).

## Adding credentials

As mentioned, the encrypted credentials are saved and encrypted in `config/credentials.yml.enc`. You can not, and should not attempt to this file directly. To add credentials in your terminal, run this command \(change the name of you editor if you need\)

```
$ EDITOR="nano" rails credentials:edit
```

If you use the Atom editor, you must pass in the `--wait` flag to make sure the encryption happens after the edits have been saved and the editor closed.

```bash
$ EDITOR="atom --wait" rails credentials:edit
```

The scaffolded `credentials.yml.enc` usually looks like the example below, with only one key \(`secret_key_base`\) active:

```yaml
# aws:
#  access_key_id: 123
#  secret_access_key: 345

# Used as the base secret for all MessageVerifiers in Rails, including the one protecting cookies.
secret_key_base: 84fd6106329678ce7c098f66b7770463f7de50658339f221f8a662d64557295e7b6977c32cba10a00a573868799d9adb04f6e783acc31ef56704161572d9ee3b
```

The credentials are stored in the YAML format and can be grouped by nesting keys.. Let's modify this by removing the comments and adding a key and a value.

```yaml
foo: 
  bar: 'foo-bar'
secret_key_base: 84fd6106329678ce7c098f66b7770463f7de50658339f221f8a662d64557295e7b6977c32cba10a00a573868799d9adb04f6e783acc31ef56704161572d9ee3b
```

When you save the file and exit your editor, you should be prompted in the terminal with the following message:

```bash
New credentials encrypted and saved.
```

## Using credentials

The key value pair we added to the credentials file is available for us to use in our application through the`Rails.application.credentials` object. To check if our addition has been encrypted properly, let's open up the rails console and try to access \(decrypt\) the value.

```ruby
$ rails c
Loading development environment (Rails 5.2.0.rc1)
[1] pry(main)> Rails.application.credentials.foo[:bar]
=> "foo-bar"
```

In, your application, whenever you need to add a credential that is sensitive, you can now call the `Rails.application.credentials` and get the value you need.

## Using the Master Key on Heroku

The easiest way to make encrypted credentials work on Heroku is to get the value stored in `config/master.key` , and use it to set up the`RAILS_MASTER_KEY` environment variable \(often referred to as **config vars** or **configuration variables**\)  of your application using the [Heroku Dashboard](https://dashboard.heroku.com).

You can also add the variable using [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli).

```bash
$ heroku config:set RAILS_MASTER_KEY=`cat config/master.key
```

The Heroku CLI command `heroku config` outputs a list of the config variables that you have added to your application. Run that command in your terminal to check if your `RAILS_MASTER_KEY` variable has been added properly.

## Using the Master Key on CI

In order to be able to use encrypted credentials on your CI Service \(SemaphoreCI, TravisCI, CircleCI or whatever your choice might be\), you need to go through a similar process as adding the Master Key to production in the form of a `RAILS_MASTER_KEY` environment variable.

**If your project is open sourced and/or a free plan on any of these services you have to make syre to encrypt the value of that variable or at the very least hide it from the test output. **

## TravisCI

There are two ways of setting up and configuring TravisCI to make use of encrypted credentials.

### Using \`.travis.yml\` configuration file

Encrypt environment variables with the public key attached to your repository using the`travis`gem:

1. If you do not have the`travis`gem installed, make sure you install it using `gem install travis`.

2. In your project folder, run:

       $ travis encrypt RAILS_MASTER_KEY=`cat config/master.key` --add env

3. Commit the changes to your`.travis.yml`.

**Note: Encryption and decryption keys are tied to the repository. If you fork and clone a project and add it to Travis CI, it will not**_** **_**have access to the encrypted variables.**

### Defining Variables in Repository Settings

On TravisCI, variables defined in repository settings are the same for all builds, and when you restart an old build, it uses the latest values. These variables are not automatically available to forks.

To define variables in Repository Settings, make sure you’re logged in, navigate to the repository in question, choose “Settings” from the side  "More options" menu, and the `RAILS_MASTER_KEY` variable in the “Environment Variables” section.

![Travis setup](https://github.com/CraftAcademy/ca_course/raw/master/assets/travis_add_env_variable.png)

## SemaphoreCI

If you use SemaphoreCI for continuous integration, you need to add a `RAILS_MASTER_KEY` environment variable in Project Settings.

Navigate to ProjectSettings -&gt; Environment Variables -&gt; Project specific Environment Variables and click the "Add +" link.

Add your variable in the panel.

![Semaphore setup](https://github.com/CraftAcademy/ca_course/raw/master/assets/semaphore_add_env_variable.png)

## Wrap up

That should set you up with encrypted credential on CI services. In another guide, I'll show you how you can configure your application to use different YAML files depending on what Rails environment you are in.

