---
title: "Role-Based Authorization"
subtitle: "Assign privileges and abilities"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Authorization, Authentication, Rails"
keywords: [Authorization, Authentication, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

### Role-Based Authorization

Almost every web application needs an authorization system if there are parts of the website that are restricted to some users. Most websites set access restrictions based on roles; that is, users are grouped by privilege. One of the most common ways to handle privileges is by implementing a system for **Role-Based Authorization**. Roles are set with attributes associated with a user account, and often implemented in a User model. For every action, the application checks the role of user that is currently using the application to check it's role to determine if access to that particular action is allowed.

In the simplest implementation, we check if a user has a specific role \(such as and Editor or Author\) and either allow access or redirect with an “Access Denied” message.

### Pundit

Pundit is an authorization library that helps us to define access rules. Pundit uses a folder named `app/policies`** **containing policies that implement access rules. Pundit is well-suited to the service-oriented architecture that is popular for large Rails applications, emphasizing object-oriented design with discrete Ruby objects providing specialized services. In this guide, we will \(eventually\) implement a role-based authorization, using Pundit.

### Roles

Note that Pundit does not implement the roles a user can have. In order to do that, we need to add an attribute to the User model ourselves, or use a gem that does it for us.

There are several ways to assign roles to a user.

We could use `boolean`'s to add roles. That approach works and can be used in systems that only have a few roles that can be assigned to a user. In the examples below, we are assuming that you already have a `User` model with Devise authentication configured. Consider this:

```ruby
user = User.create(email: 'author@newsroom.com', password: 'password', author: true, visitor: false)
```

Now, let's consider that we want to add more roles? How many booleans can we handle on our model before it gets hard to maintain?

Another strategy would be to add a `role` attribute like this:

```bash
$ rails g migration add_role_to_users role:string
```

Set the new default column value to ''visitor' by editing the new migration file created.

```ruby
class AddRoleToUsers < ActiveRecord::Migration
  def change
    add_column :users, :role, :string, default: 'visitor'
  end
end
```

Here, we are adding the role visitor as the default role of all users, unless we expicitely state any other role.

Our User class would look like this:

```ruby
class User < ApplicationRecord
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  # instance methods that will help us keep our code shorter when we check for a role
  def author?
    self.role == 'author'
  end

  def visitor?
    self.role == 'visitor'
  end
end
```

### Using Active Record `enum`

There's also another way of setting this up, using `enum`.

```ruby
class AddRoleToUsers < ActiveRecord::Migration[5.2]
  def change
    add_column :users, :role, :integer
  end
end
```

And the the `User` class:

```ruby
class User < ApplicationRecord
  after_initialize :set_default_role, if: :new_record?

  enum role: [:visitor, :author]

  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable


  private

  def set_default_role
    self.role ||= :visitor
  end
end
```

This approach stores the value as an integer \(based on the index number of the value in the array we add to `roles:`\) and has the advantage to give us some methods for free.

We can go into the console and try things out.

```ruby
rails c
Loading development environment (Rails 5.2.0.rc1)
[1] pry(main)> user_1 = User.create(email: 'author@newsroom.com', password: 'password', role: :author)
=> #<User id: 5, email: "author@newsroom.com", created_at: "2018-03-21 11:14:01", updated_at: "2018-03-21 11:14:01", role: "author">
[2] pry(main)> user_1.author?
=> true
[3] pry(main)> user_1.visitor?
=> false
```

We now also have a bang method available, that will change the role of the user:

```ruby
[4] pry(main)> user_1.visitor!
   (0.2ms)  BEGIN
  User Update (0.5ms)  UPDATE "users" SET "updated_at" = $1, "role" = $2 WHERE "users"."id" = $3  [["updated_at", "2018-03-21 11:15:12.882379"], ["role", 0], ["id", 5]]
   (1.3ms)  COMMIT
=> true
```

That's pretty handy, right? You can read more about `enum` on the [Rails API documentation]( http://api.rubyonrails.org/classes/ActiveRecord/Enum.html) site.

### It all starts with a test...

```gherkin
Feature: User can create article with image attachment
  As an Author
  In order be able to add content to the news service
  I would like to be able to publish articles with an image

  Background:
    Given the following users exist:
      | email                 | role    |
      | author@newsroom.com   | author  |
      | random_guy@random.com | visitor |

  Scenario: Author creates an article
    Given I am logged in as "author@newsroom.com"
    Given I am on the create article page
    And I fill in "Title" with "Awesome news"
    And I fill in "Content" with "Lorem ipsum"
    And I attach a file
    And I click "Create Article"
    Then I should be on the article page for "Awesome news"

  Scenario: Visitor tries to create an article
    Given I am logged in as "random_guy@random.com"
    Given I try to visit the create article page
    Then I should be redirected to the index page
    And I should see "You are not authorized to perform that action!"
```

### Testing Policies

Install the [`pundit-matcher`  gem](https://github.com/chrisalley/pundit-matchers)  and require it in your `spec_helper` \(see the Gem documentation\)

Add the following specs to your \`spec/policies/article\_policy\_spec.rb\` \(this is testing my implementation, you might need to modify your specs to reflect your code\)

```ruby
describe ArticlePolicy do
  subject { described_class.new(user, article) }

  let(:article) { create(:article) }

  context 'user is a visitor' do
    let(:user) { create(:user, role: 'visitor') }

    it { is_expected.to permit_actions [:show, :index] }
    it { is_expected.to forbid_new_and_create_actions }
  end

  context 'user is an author' do
    let(:user) { create(:user, role: 'author') }

    it { is_expected.to permit_new_and_create_actions }
  end
end
```

### The ArticlePolicy

```ruby
class ArticlePolicy < ApplicationPolicy
  class Scope < Scope
    def resolve
      scope
    end
  end

  def index?
    true
  end

  def new?
    @user.author?
  end

  def create?
    new?
  end
end
```



