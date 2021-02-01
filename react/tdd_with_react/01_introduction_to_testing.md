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
