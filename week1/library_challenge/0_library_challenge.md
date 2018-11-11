---
title: "Ruby basics - Library Challenge"
author: [Craft Academy]
date: Version 2.1 - 2018
subject: "Ruby"
keywords: [Ruby, Example, Learn To Code]
subtitle: "Your First Weekend Challenge"
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Library Challenge
It's time for you to code on your own. With the ATM Challenge solved, it's time for you to put your new skills and experience into practice. This WILL be a tough one, we know that. Don't despair if you find it extremely hard. It IS hard. All we ask of you, is that you do your best and try to solve as much of the challenge as you possibly can. That's all.

## Instructions

Read this entire README carefully and follow all instructions. Go to [github.com/CraftAcademy/library-challenge](https://github.com/CraftAcademy/library-challenge) and fork that repository to your own GitHub account.

* Challenge time: entire weekend, until Monday morning stand-up/scrum (at 9am)
* Feel free to use Google, Stack Overflow, your notes, previously written code, books, etc. but work on your own
* If you refer to, or have in whole or partially used, the solution of another coach or student, please put a link to that in your README
* If you have a partial solution, **still check in a partial solution to GitHub and create a Pull Request**
* You must submit a Pull Request to this repository with your code by 9.30am Monday morning - before the stand-up


## Learning objectives

This challenge will provide you with an opporunity to practice a lot of your newly acquired technical skills.

* Practice TDD to drive your development process
* Adopt to a new domain
* Work with data stored in hashes and perform 
* Learn about YAML
* Learn about storing information in a text file
* Practice writing documentation

## Requirements

Write a Library program with the following user stories:

```
As an individual
In order to get my hands on a good book
I would like to see a list of books currently available in the library
with information about the title and author
```

```
As a library
In order to have good books to offer to the public
I would like to be able to have a collection of books stored in a file
```

```
As a library
In order to have good books to offer to the public
I would like to be able to allow individuals to check out a book
```

```
As a library
In order to make the books available to many individuals
I would like to set a return date on every check out
and I would like that date to be 1 month from checkout date
```

```
As an individual
In order to avoid awkward moments at the library
I would like to know when my book is supposed to be returned
```

## Action Points

* Fork the challenge repo: https://github.com/CraftAcademy/library-challenge
* Run the command `bundle install` in the project directory to ensure you have all the gems installed
* Write your specs and implementation code
* Be smart about using Git: commit and push often. Use feature branches.
* Create a Pull Request as soon as possible
* Read the comments from Hound and fix any issues that the service points out.


## Some hints:
  * A Person needs to have a list of books that he currently has in his possession. That list needs to include the return date.
  * The return date can be calculated using the `Date` object. Out of the box, there are methods you can use to add days to the current date.
  * Make use of `doubles` when writing your specs
  * Follow the [naming conventions/standards](https://craftacademy.gitbooks.io/coding-as-a-craft/content/extras/naming_standards.html) for methods and variables

## What we are looking for

We are hoping to see that:
* You can take a problem set and write a well tested implementation on your own.
* You understand how to define Ruby Classes and work with objects.
* You understand how classes can interact with each other.
* You know how to make use of arrays, hashes, and associated methods to create dynamic lists.
* You know how to write specs and use them as a blueprint in your development.
* I can track your work by following you commit history - so please commit as soon you are done with a feature or when you have made a test pass. 

In your Pull Request, we are hoping to see:
* That you are testing the right thing in the right spec file.
* That all tests passing - green is good!
* High test coverage (above 95% is accepted)
* The code is easy to follow: every class has a clear responsibility, methods are short, code is nicely formatted, etc.
* The README.md includes information on how to use your solution with command examples in `irb`. 


**Happy coding!**
