---
title: "Ourside In - Acceptace Unit Test Cycle With ReactJS"
author: [Craft Academy]
date: Version 1.0 - 2019
subject: "ReactJS"
keywords: [React, TDD, Learn To Code]
subtitle: "Testing Code You Wish You Had - BDD style"
titlepage: true
titlepage-color: 96006a
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
cover-image: react_tdd_cover.jpg
...

## Purpose
The purpose of this guide is to introduce you to a development flow that employs an Outside-In approach to developing features. 

_"Outside–in Development focuses on satisfying the needs of stakeholders. The underlying theory is that to create successful software, the team must have a clear understanding of the goals and motivations of the stakeholders. The ultimate goal is to produce software that is highly consumable and meets or exceeds the needs of the intended client."_

In order to get set up for the flow, we will need some tools that will give us the functionality we need. Let's take a look at what we want to achieve so that we can choose the right tool for the job.

One of the first things we will do is to build some sort of a user interface. Why? Well, we need to create an entry point for somebody that want to use our application. "But there is no application yet..", you say. That's correct. It's our job to build it.

We will start assuming that we have a fictional application when we write our test, and then we will make it a reality by writing our implementation and building it.

That's the theory. Now, let's get after it. 

The "Outside" part will be covered by Acceptance tests. They are sometimes referred to as End-to-end (e2e) tests. Simply put, these tests cover the entire stack and aim to provide an answer to questions like "If I, as a user, fill in this field with some value, and click on this button, then the effect should be..."

The "In" part (or rather the "Inside" part) will be covered by Unit tests that aim to help us make sure that the code we write (modules, components, services, etc...) actually do what we want them to do. There are many ways to write Unit tests, and we will try to keep them as simple as possible. For me, the important part is that the process of writing the specifications (tests are often referred to as "specs"), help us make decitions and guide us when we build our units.

## Acceptance-Unit Test Cycle

The concept of Acceptance-Unit Test Cycle is pretty simple. You describe what you want the system to do by describing potential users interactions with the different parts of the application. You work outside-in to implement features using the examples to validate that you’re building the right thing at the right time. In this post, I would like to show you this workflow in action and let you experience, at least partially, its benefits.

I will try to show you an approach that combines high-level acceptance tests and low-level unit tests to both drive the development process and make sure that we build a robust and well-structured application.

A good cycle to follow is this Outside-in approach:

1. Write a high-level (Outside) business value example that goes red 
2. Write a lower-level (Inside) example for the first step of implementation that goes red 
3. Implement the minimum code to pass that lower-level example, see it go green
4. Write the next lower-level  example pushing towards passing step 1
5. Repeat steps 3 and 4 until the high-level test (1) goes green
6. Start over by writing a new high-level test

During the process think of your red-green state as a permission status:

When your low-level tests are green, you have permission to write new examples or refactor existing implementation. You must not, in the context of that refactoring, add new functionality/flexibility.

When your low-level tests are red, you have permission to write or change implementation code only for the purpose of making the existing tests go green. 

You must resist the urge to write the code to pass your next test, which doesn’t exist, or implement features you’ll need "someday."


