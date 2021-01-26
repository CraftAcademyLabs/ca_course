---
title: "Test Driven Development With ReactJS"
author: [Craft Academy]
date: Version 1.0 - 2019
subject: "ReactJS"
keywords: [React, TDD, Learn To Code]
subtitle: "Testing Code You Wish You Had"
titlepage: true
titlepage-color: 96006a
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
cover-image: react_tdd_cover.jpg
...

# Preamble
Software testing plays an important role in the process of verifying software functionality and preventing bugs in production code. By writing automated tests using code instead of conducting manual tests, the amount of tedious work during the development process can be reduced and the software quality can be improved.

Software testing is a process used for increasing the quality of a software or a product and for improving it by identifying defects, problems, and errors. It is considered by many as the most important phase of the whole process of software development.

There are many different types of testing that you can use to make sure that changes to your code are working as expected. We will be going over the concepts in more details, exploring the pros and cons of them,  before we start practicing some of them.

# Manual vs. Automated testing

At a high level, we need to make the distinction between manual and automated tests. Manual testing is done in person, by clicking through the application or interacting with the software and APIs with the appropriate tooling. This is very expensive as it requires someone to set up an environment and execute the tests themselves, and it can be prone to human error as the tester might make typos or omit steps in the test script.

Automated tests, on the other hand, are performed by a machine that executes a test script that has been written in advance. These tests can vary a lot in complexity, from checking a single method in a class to making sure that performing a sequence of complex actions in the UI leads to the same results. It's much more robust and reliable than automated tests – but the quality of your automated tests depends on how well your test scripts have been written.

Automated testing is a key component of concepts like Continuous Integration and Continuous Delivery and it's a great way to scale the QA process.

# Software Testing Overview

Software testing aims at obtaining its intentions (both implicit and explicit), but it does have certain flaws. Still, testing can be done more efficiently if certain established principles are followed.

Without proper testing, none of the application’s stakeholders can be sure whether it works as expected or not.

In spite of having constraints, software testing extends to control other verification techniques like static interpretation, model checking, and data. It is necessary to understand the goals, limitations, and principles of software testing so that its effectiveness can be increased.

If the client gets their software without any testing and full of bugs and errors, he can stop or dismiss all contracts. This work examines an alternative procedure, the main purpose of which is to divide testing over all the lifecycle of software, starting from taking of requirements and continuing throughout development and maintenance stages.

Such a thorough transmutation of the useful post-constructional testing (Test Last or Test Afterward) turns it into something else but testing. Such a process stops from being a testing activity and becomes Test Driven Development.

# Test Driven Development (TDD)
Test Driven Development (TDD) is a programming practice which combines Test First Development (TFD) with refactoring. TDD involves writing a test for a code before the actual code, followed by the code to pass the pre-written test.

It is a cyclic development technique. Each cycle coincides with the implementation of a small feature. Each cycle is said to be complete once the unit-tests rendering a feature, as well as all the existing reversion tests, pass.

In TDD, each of the cycles starts by writing a unit test. This is followed by the implementation necessary to make the test pass and conclude with the refactoring of the code in order to remove any duplication, replace transitional behavior, or improve the design.

TDD is a key practice of extreme programming, it prescribes that the code is developed or changed exclusively on the basis of the unit test results. The first developer creates the classes of the system together with the correspondent class interfaces.

Then, the developer writes the test case for each class. Finally, each of the components is involved in the testing process, consisting of two activities: to execute the tests and, when some of them fail, to refactor the code in order to remove the bugs, duplicity & code smells which are supposed to be the cause of the failure. The process is said to be over when all the tests succeed.

## Step By Step
TDD can be compiled in the following three steps:

#### Step 1 – Write a failing test

Write a test for your program keeping in mind all the possible challenges it can face. As there is no code yet, it’s definitely going to fail.

#### Step 2 – Make the test pass

Write the minimum amount of code for your test, in order to make the test pass.

#### Step 3 – Refactor

Improve the code quality of your code without affecting the pre-written and new test results (i.e, already passed tests).

# Testing web applications
Web applications typically share multiple properties with traditional software, i.e. software that runs as an application locally on a single computer. There is however some key differences and features exhibited by web applications, each of them contributing to different challenges when doing software testing.

* It can be used by a large number of users from multiple geographical locations at the same time.
* The execution environment is very complex and may include different hardware, web servers, Internet connections, operating systems and web browsers.
* The software itself typically consists of heterogeneous components, which often uses several different technologies. For example it may have a server component that uses a back-end framework (like SpringBot, NodeJS or Ruby on Rails), an client components that uses HTML, CSS and Javascript or a front-end framework (like Angular, VUE, ReactJS, etc.).
* Some application components, such as parts of the graphical interface, may be generated at run time depending on user-input and the current state of the application.

Web applications run in a browser and should be tested in a browser. Tests executed in a browser are, however, slow. Mostly due to the level of testing, but also due to the overhead of executing scripts, rendering pages and loading images. Another downside is that it requires a full graphical desktop environment and thus may take some effort and resources to set up on a server.

## Headless browser

One way of achieving higher test execution speed is to use a headless browser. A headless browser typically does not have any graphical interface and therefore does not require a graphical desktop environment in order to gain speed. It used to mean that you ware spinning up a web browser where most functionality has been stripped away. Nowdays, we can use Google's Chrome Driver - essentially bring all modern web platform features provided by Chromium and the Blink rendering engine to the command line.

However, browser tests often tend to be bound to the HTML and the CSS classes of the tested page, since we need to locate elements in order to click buttons or fill in forms. This makes the tests fragile since changes in the user interface may break them, and if the same elements are used in many tests, we may need to go over large parts of our test suite.


# Acceptance-Unit Test Cycle (AUT)
Building applications for the web involves much more than just creating components/units. We want to extend the tests to include acceptance tests and employ a combination of Acceptance and Units tests in our development flow. We will be covering this protocol in more detail and take a closer look at the toolchain that can support an effective use of tests.

## The workflow...

The concept of Acceptance-Unit Test Cycle is pretty simple. You describe what you want the system to do by describing potential users interactions with the different parts of the application. You work outside-in to implement features using the examples to validate that you’re building the right thing at the right time. In this post, I would like to show you this workflow in action and let you experience, at least partially, its benefits.

I will try to show you an approach that combines high-level acceptance tests and low-level unit/component tests to both drive the development process and make sure that we build a robust and well-structured application.

A good cycle to follow is this **outside-in approach**:

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

# Are Manual Test Not Needed?


Before we move on, there is one topic that I feel we need to cover, and that is the role of manual tests. These are tests performed by youself and your collegues, if not by a group of testers in your department or team.

Let's be honest, all software WILL be tested at some point and all bugs or inconsistencies WILL be exposed. Either by you or by your end-users.

Manual testing provides a way to quickly evaluate a product and provide testers with a familiarity of the features during the development process. Testers create test cases based on their ability to determine whether or not requirements are met. But initially these test cases have to be executed manually, both for the sake of verification of the necessary steps, and also in order to record test scripts for automation in the future.

Advantages of Manual Testing

1) **Programming Knowledge is not required**

In Manual Testing, Understanding Requirements, Documenting Test Cases and Executing Test Cases are the important tasks, but Programming is not used.If it is Test Automation every tool uses anyone Programming language to enhance test cases, so programming knowledge is mandatory for Test Automation.

2) **Recommendable for Dynamically changing GUI designs**

If the User Interface of the application changes dynamically then test automation is not recommendable.

3) **Manually Testing Tests From a Human Perspective**

Human testers can quickly identify when something looks "off". Automated tests don’t pick up these visual issues. When a tester interacts with software as a user would, they’re able to discover usability issues and user interface glitches. Automated tests can’t test for these things.

4) Exploratory Testing Can Only Be Done Manually

Automated tests only perform the actions that you tell them to. They require planning and preparation to write, which restricts the test to certain boundaries. These boundaries mean there isn’t any room to stray from the written test to truly “explore” the application. Exploratory testing (or adhoc testing) gives us the opportunity to answer questions like, “what happens if I do this?” It enables us to carve our own path throughout the test with little to no boundaries.

5) Automated Tests Can Contain Errors and Holes

Just like code can have bugs, automated test scripts can also have bugs. This means that automated testing has the potential to report false positives and false negatives. By including a human touch throughout the testing process, these errors are avoided.

6) Some Scenarios Are Not Technically Feasible to Automate or Cost Too Much

Let’s take, for example, an iPad app that relies heavily on tap gestures. Automating the “tap” can not only be costly but also may not be the most accurate test compared to a human’s finger touching the user interface. It often makes more sense to manually test certain features. By the time you find a resource to write an automated test, a manual test could be completed with bugs already fixed. Sometimes, it makes more sense to just go the “manual” route.

7) Manual Testing Helps Us Understand the Whole Problem

Over time, automated testing can save time. It is great for getting quick results on a broad level, but manual testing allows us to understand the problem on a conceptual and emotional level. It connects us with the end-user and introduces us to a level of empathy automated testing doesn’t provide.
