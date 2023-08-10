# Setting up a project
## Rails with testing frameworks and CI/CD

This week we would like to assess your ability to scaffold a new Rails API project. 

**Assessment time - up to 30 minutes**

As you work on this exercise, please '_**think aloud**_'. That means verbally describing your mental process as it develops, including the doubts and questions you have, the solution strategies you consider, and the reasons that justify your decisions.

### Think Aloud Clarification

The 'Think-aloud' protocol involve the participant thinking aloud as s/he is performing a set of specified tasks or working on an excercise. The participant is asked to say whatever comes into mind as s/he completes the task. This might include what s/he is looking at, thinking, doing, and feeling. This gives the coach valuable insight into the participant's cognitive processes (rather than only their final product), to make thought processes as explicit as possible during task performance. Sessions should be recorded so that coaches can go back and refer to what participants did and how they reacted.


## Challenge

Your project team will be starting a new project - a crowdsourced transportation platform called **Unter**. You were assigned the following API chores by your team:

### Set up main repository
**Description**


```gherkin
As a development team
In order to be able to collectively work on the same codebase
We would like to have a main repository on GitHub we all can use as the upstream repository
```

**Tasks (Acceptance Criteria)**

- [ ] Set up a repository on GitHub (Please treat your own account as the account for this organization for the sake of this exercise)
- [ ] Create a `development` branch and set it as the main branch of the project
- [ ] Share repo with team

### Set up application structure

**Description**

```gherkin
As a development team
In order to be able to start adding features
We would like to have an basic application structure 
```

**Tasks (Acceptance Criteria)**

- [ ] Scaffold a Rails API application
- [ ] Turn off generators for helpers, assets and all specs (accept model specs)
- [ ] Clean up the generated code from unnecessary comment for good readability


### Set up testing environment

**Description**

```gherkin
As a development team
In order to be able to make sure our code is doing what it is supposed to do
We would like to be able to write and run automated tests
```

**Tasks (Acceptance Criteria)**

- [ ] Add and configure Rspec for Unit and Request specs
- [ ] Configure shoulda matchers

### Set up Continuous integration

**Description**

```gherkin
As a development team
In order to make sure that the code we add to the code base is functional
We would like to run full test suite on a remote service
```

**Tasks (Acceptance Criteria)**

- [ ] Set up CI on Semaphore

### Set up Test Coverage Metrics
**Description**

```gherkin
As a development team
In order to see how much of our code is covered in tests
We would like to measure test coverage using a remote service 
```

**Tasks (Acceptance Criteria)**

- [ ] Set up Coveralls for the main repository
- [ ] Add and configure Coveralls for RSpec

### Set up Continious Deployment
**Description**

```gherkin
As a development team
In order to automate the deployment process during development
We would like all code that has been merged into the main code base, to be deployed to a server
```

**Tasks (Acceptance Criteria)**

- [ ] Create an application on Heroku
- [ ] Set up deployment using the CI tool