# Acceptance-Unit Test Cycle

The concept of Acceptance-Unit Test Cycle is pretty simple. You describe what you want the system to do by describing potential users interactions with the different parts of the application. You work outside-in to implement features using the examples to validate that you’re building the right thing at the right time. In this post, I would like to show you this workflow in action and let you experience, at least partially, its benefits.

I will try to show you an approach that combines high-level acceptance tests and low-level unit tests to both drive the development process and make sure that we build a robust and well-structured application.

A good cycle to follow is this outside-in approach:

1. Write a high-level (outside) business value example (using Puppeteer/Cypress) that goes red 
2. Write a lower-level (inside) example (using Jest/Enzyme) for the first step of implementation that goes red 
3. Implement the minimum code to pass that lower-level example, see itgo green
4. Write the next lower-level  example pushing towards passing step 1
5. Repeat steps 3 and 4 until the high-level test (1) goes green
6. Start over by writing a new high-level test

During the process think of your red-green state as a permission status:

When your low-level tests are green, you have permission to write new examples or refactor existing implementation. You must not, in the context of that refactoring, add new functionality/flexibility.

When your low-level tests are red, you have permission to write or change implementation code only for the purpose of making the existing tests go green. 

You must resist the urge to write the code to pass your next test, which doesn’t exist, or implement features you’ll need "someday."
