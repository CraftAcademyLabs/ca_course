The concept of the Acceptance-Unit Test Cycle is pretty simple. You describe what you want the system to do by describing potential user’s interactions with the different parts of the application.

You work outside-in to implement features using the examples to validate that you’re building the right thing at the right time. During this workshop, you will see this workflow in action and will experience, at least partially, its benefits.

For this workshop, we will use Ruby on Rails as the framework for our application. But the workflow can be applied to any stack or framework of your choice.

A good cycle to follow is this outside-in approach.

It means starting with views and working your way in toward the models.

By developing from the outside in, you are always taking a client perspective at each layer of the application.

The result is an absolute minimum implementation, consisting of simple, expressive interfaces.

Starting with a high-level specification requires you to have a clear understanding of what you want to achieve. In the case of the Legacy Cade Challenge, it should be rather unproblematic - the application is all there for you to explore. In “real” projects, when you will be defining features and interfaces from scratch, it will be more complex and problematic. All will come to place as we move forward in the camp, but for now, let me just point out that communication (a simple conversation with the client) will be the solution to this problem.

Outside-in development often feels strange to newcomers. Most developers prefer to start with the “important” part of an application, i.e., the domain model, and work their way outwards.

Thinking like a server and not a client can lead to overengineering by implementing more than you need.

Your resulting objects and their interfaces will also be less than optimal, or at least take longer to get quite right.

• Write a high-level (outside) business value example (using Rspec) that goes red

• Write a lower-level (inside) RSpec example for the first step of implementation that goes red

• Implement the minimum code to pass that lower-level example, see it go green

• Write the next lower-level RSpec example pushing towards passing step 1

• Repeat steps 3 and 4 until the high-level test (1) goes green

• Start over by writing a new high-level test



During the process think of your red-green state as a permission status:

• When your low-level tests are green, you have permission to write new examples or refactor existing implementation. You must not, in the context of that refactoring, add new functionality/flexibility.

• When your low-level tests are red, you have permission to write or change implementation code only to make the existing tests go green. You must resist the urge to write the code to pass your next test, which doesn’t exist, or implement features you’ll need “someday.”



The tools we will be using to test our application is RSpec. We are going to use Rspec to write both unit and acceptance tests. By now, you’ve had a good bit of experience with Rspec in your previous projects, so this is not entirely new to you.