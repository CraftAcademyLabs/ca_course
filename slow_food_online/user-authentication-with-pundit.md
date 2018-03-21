### Role-Based Authorization

Almost every web application needs an authorization system if there are parts of the website that are restricted to some users. Most websites set access restrictions based on roles; that is, users are grouped by privilege. One of the most common ways to handle privileges is by implementing a system for **Role-Based Authorization**. For every action, the application checks an attribute of user that is currently using the application to check it's role to determine if access to that particular action is allowed. 

In the simplest implementation, we check if a user has a specific role \(such as administrator\) and either allow access or redirect with an “Access Denied” message. Roles are attributes associated with a user account, and often implemented in a User model. 

### Pundit

Pundit is an authorization system that uses simple Ruby objects for access rules. Pundit uses a folder named `app/policies`** **containing policies that implement access rules. Pundit is well-suited to the service-oriented architecture that is popular for large Rails applications, emphasizing object-oriented design with discrete Ruby objects providing specialized services.







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

### Testing

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





