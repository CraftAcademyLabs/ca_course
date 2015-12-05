## Your first website

It's all about the web. During this course we'll be building a lot of applications - to start with we'll be writing small pieces of software that will be run on our own computers. Relatively soon we'll move on to more advanced applications built to be deployed an run on a web server.

The idea is that you get to master the different techniques before we introduce another layer of complexity. We have to learn how to crawl before we can run an marathon.

In this exercise we will start scratching the surface of html and css and build a small static web page - and deploy it on the internet for everybody to see. We'll also get a chance to practice some git-skills - an essential part of your skill-set. 

** Note: *The following walk-through inspired by the step-by-step guide you can find on https://pages.github.com/* **

All of us are using GitHub for storage of our code in the cloud and for collaboration. If you still don't have a GitHub account - then you should definitely [head over to their site](https://github.com) and do it ASAP.

One of the many features of GitHub offers is the possibility of to create web sites for users and projects. We'll take advantage of that feature to publish a personal web site.

Head over to GitHub and create a new repository named `username.github.io`, where username is your username on GitHub. In my case it would be:  `tochman.github.io`. If the first part of the repository name doesn’t exactly match your username, it won’t work, so make sure to get it right.

![](../images/github_io_step1.png)

In your terminal, go to the folder where you want to store your project, and clone the new repository:

```shell
$ git clone https://github.com/username/username.github.io
$ cd username.github.io
$ echo "Hello World!" > index.html
$ echo "I'm <your name>, and I attend the Craft Academy Bootcamp" >> intex.html
```

Use Git to commit, and push your changes to GitHub:
```shell
$ git add .
$ git commit -m "initial commit"
$ git push origin master
```

** Open your browser and go to `username.github.io`. **

You have just deployed your first web site. 


