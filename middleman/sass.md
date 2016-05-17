# SASS

We will grab the foundation SASS files from the Git repo for the `middleman-zurb-foundation` gem. 

A good thing is that GitHub repositories can be accessed from both Git and Subversion clients. So I can make use of a specific feature that is available to me in SVN but not in Git. That's what I'm gonna do to solve my problem.

Let's move on...

So, I need to make sure that I have Subversion installed on my system. I'm on a Mac with homebrew installed, so I go with:

```
$ brew install subversion
```

If you run into permission issues I recommend this [SO post](http://stackoverflow.com/questions/19433419/how-to-install-subversion-using-homebrew-on-osx).

For Linux systems, etc you'll find all relevant info on [https://subversion.apache.org/packages.html](https://subversion.apache.org/packages.html).

Once you have Subversion installed, navigate to the `/source` folder and run the following commands.

```shell
$ svn export https://github.com/axyz/middleman-zurb-foundation/trunk/source/javascripts javascripts/ --force

$ svn export https://github.com/axyz/middleman-zurb-foundation/trunk/source/stylesheets stylesheets/ --force

$ svn export https://github.com/axyz/middleman-zurb-foundation/trunk/source/layouts layouts/ --force

```

