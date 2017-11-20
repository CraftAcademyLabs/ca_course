## Step 1 - Setting up the project


We have set up a [repository for the project](https://github.com/CraftAcademy/slow_food_sinatra_ar) with a basic, modular Sinatra application scaffold. Your team should make use of that code and pull it in. Fork that repo and go over the code and the gems already included in the `Gemfile`.  

Your very first assignment is the get that code working and run the application on your development machines. Make use of all the previous challenges to find answers to obstacles you might encounter.

#### RVM help
You will find that you need to use a different version of Ruby to run this code. Run:
```shell
$ rvm use 2.4.1
```
This will prompt you to run some more commands. Run them. You'll need to bundle before you can do anything else. But what happened to your `bundle` command!? 

You've just changed to a new version of Ruby. That means you need to re-install all of your gems, including bundler. Run:
```shell
$ gem install bundler
```
Now you have `bundle` yet again, and install all the gems in your `Gemfile`.

**Remember, as with every other material, there might be errors in the provided code. It's up to you to find and correct them if you choose to use the provided snippets. Don't come blaming the authors of this challenge if you run into trouble 😉**

**Remember, it's not just about the code. This challenge is about practicing the workflow, solving problems, learning how collaborate as a team and much more.**


### Agile principles

There are different Agile practices you can use in this and other projects, and we will be talking a lot about that and introducing different agile techniques as we move along in the course. For now, you should always follow these core Agile principles:

* Focus on user needs
* Deliver iteratively
* Keep improving how your team works
* Fail fast and learn quickly
* Keep planning and stick to your plan

Remember, XP supplements the Scrum process with practices like:

* Automated unit testing
* Refactoring
* Continuous integration
* Behavior Driven Design
* Test Driven Development


### Planning and Communication

Your team need to plan together, review these plans regularly and change them based on your progress and any new facts and requirements that arise during the development process. 

Both [Waffle.io](https://waffle.io/) and [ZenHub.io](https://www.zenhub.io/) are project management tools powered by your GitHub Issues & Pull Requests. Waffle.io is the tool of choice for many projects and provides a web based interface for managing Issues and track Pull Requests. ZenHub provides similar functionality (and more) as a browser extension. It is up to you to decide what tool your team want to use.

### Structure

We will make use of 4 columns in Waffle (or ZenHub if you choose to use that tool) to track our work. 

![](/images/waffle_io.png)

#### Backlog
Any issue that we see as important for the near future lives in the **Backlog**. Issues in this column are generally prioritized from top to bottom with the top items having the highest priority.

#### Ready
Any issue that we would like to work on soon is moved to **Ready**. As we move ahead, we pull issues from Backlog into Ready, so we have an idea of the most important items to work on for at the moment.

#### In Progress
Any issue that is currently being worked on is put into the **In Progress** column and assigned to a developer. By limiting the number of issues you work on at one time helps you focus on what needs to be done.

#### Done
Any issues that have been closed are reflected in the **Done** column. For pull requests, this means that the code has been merged and deployed.

### Stories
We will use 3 types of stories in our project plan: Features, Chores and Bugs

#### Features
Features are stories that provide verifiable business value to the team's customer. Examples of features include "add a 'special instructions' field to the checkout page", "purchase history should load in half a second", and "add a new method addToInventory to the public API". Features are worth points and therefore must be estimated.

#### Chores
Chores are stories that are necessary, but provide no direct, obvious value to the customer. Examples include "sign up for access to geocoding service" and "Find out why the detailed test suite takes so long". Chores can represent "code debt", and/or points of dependency on other teams.

####Bugs
Bugs represent unintended behavior that can be related to features, for example "login box is wrong color", and "price should be non-negative".

Introduction to Waffle.io: https://youtu.be/yEbRaA3rYuA

Introduction to ZenHub.io: https://youtu.be/TRu7vKCg920





