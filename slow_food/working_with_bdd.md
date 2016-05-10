## Step 6 - Working with BDD

Working with BDD

The concept of **Behaviour Driven Development** (BDD) is pretty simple. You describe what you want the system to do by describing potential user's interactions with different parts of the application. You work outside-in to implement features using the examples to validate that you're building the right thing at the right time. 

During this week you will see BDD in action and will experience, at least partially, the benefits of this method.

The tools we will be using to test our application are **RSpec** for our unit tests and **Cucumber** for our acceptance tests. Those are the two so called **testing frameworks** that will help us to write good code. 

Cucumber (cucumber.io) is a testing framework used to describe high level functionality of your application. We will refer to them as acceptance test or features. One of Cucumber's most compelling features is that, it allows you to write these descriptions in plain text. Even in your native language.

During the development process, we'll take an approach that combines high level acceptance tests and low level unit tests to both drive the development process and make sure that we build a robust and well structured application.

###Test first - as we always do

Let's start with writing some high level acceptance tests.

What we want to do at this stage is to get the user stories we have defined together with our LoFi's and look at them from a user's perspective but also what an actual implementation of features would look like. 

In short, we want to take each user story and break it down to scenarios that each represents a use case in the application. Sounds confusing? It is, but look at it as a form of a blue print that you will use when we start to actually build the app.



Cucumber is not installed on your system so we need to do that as a first step (see the Setup section)


