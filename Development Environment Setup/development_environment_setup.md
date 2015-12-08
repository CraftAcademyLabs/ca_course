## Development Environment Setup

One of the first things you need to do before you start going through the challenges in this course is to set up the environment with all the applications you need to be able to write and run code. This is optional though. 

Every step in this material can be completed using a hosted development enviroment. If you want to know more about Cloud services and how to set them up, please see chapter [Working in the Cloud](/working_in_the_cloud.md)

Alright, let's focus on how to set up your local development environment. We'll be covering both OSX and Linux, so jump to whatever section suites you. 

Are you a Windows user? Well, two things; '*I'm sorry to hear that*' and '*Please see chapter [Working in the Cloud](/working_in_the_cloud.md)*'

### OSX

Launch the Terminal, found in `/Applications/Utilities/`

#### Xcode Command Line Tools
[Xcode](https://developer.apple.com/xcode/) is an integrated development environment (IDE) with a suite of tools for developing software for OS X and iOS. But Xcode is more than what just meets the eye. Ruby gems often use so called "native extensions", that connects them with other non-Ruby components on your machine. Without Xcode installed, you don't have a C/C++/Objective-C compiler installed and can not use those extensions. While installing Xcode you also install LLVM, GCC, and many other developer tools. 

For the purpose of this course, we are going to install Xcode Command Line Tools, a part of the Xcode IDE, without installing the entire Xcode package. 

Check if Xcode Command Line Tools are installed:
```shell
$ xcode-select -p
```

Install:
```shell
$ xcode-select --install
```
![Step1](../images/xcode_clt_step1.png)

![Step2](../images/xcode_clt_step2.png)

![Step3](../images/xcode_clt_step3.png)

![Step4](../images/xcode_clt_step4.png)


Verify installation:
```shell
$ xcode-select -p
/Library/Developer/CommandLineTools
# or: 
/Applications/Xcode.app/Contents/Developer
```

Also, verify that LLVM-GCC is installed:

```shell
$ gcc --version
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/usr/include/c++/4.2.1
Apple LLVM version 7.0.0 (clang-700.1.76)
Target: x86_64-apple-darwin14.5.0
Thread model: posix
```
#### Homebrew
[Homebrew](http://brew.sh/) is “the missing package manager for OSX” written in Ruby. It allows you to easily install hundreds of open-source tools. Homebrew complements OSX. Install your gems with `gem install`, and their dependencies with `brew install`.

In your terminal, run the following command:
```shell
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Once the installation is successful, run the following command:

```shell
$ brew doctor
```
If you get the response `Your system is ready to brew`, you're all set.

#### RVM

Ruby Environment Manager (RVM) is a tool that allows you to install, manage and work with multiple ruby environments. RVM lets you deploy each project with its own completely self-contained and dedicated environment, from the specific version of ruby, all the way down to the precise set of required gems to run your application.

To install RVM, run the following command in your terminal:

```shell
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
$ curl -L https://get.rvm.io | bash -s stable --auto-dotfiles --autolibs=enable --ruby
```
Once the installation is complete you must load RVM to make RVM available in your current session. Otherwise you'll need to restart your terminal. Run the following command in terminal:

```shell
$ source ~/.rvm/scripts/rvm
```

Verify your install:

```shell
$ type rvm | head -1
rvm is a function
```

If you get the response `rvm is a function` it means you're all good.

Now you can check what ruby version/versions you have installed on your machine with the `list` command:

```shell
$ rvm list
```

And install new rubies with:
```shell
$ rvm install 2.2.0  #or whatever version you need
```

Switching between versions is done with the `use` command:

```shell
$ rvm use 2.2.0
```
Read the [RVM documentation](https://github.com/rvm/rvm#action) to see all the different actions you can use.

#### Bundler
Bundler is a program for managing gem dependencies in your Ruby projects. With Bundler you can specify which gems your program needs, what versions they should be at, it can help you install them, load them at runtime and distribute them with your software.

*"Bundler provides a consistent environment for Ruby projects by tracking and installing the exact gems and versions that are needed."* - http://bundler.io/

You install it with a terminal command:

```shell
$ gem install bundler
```

Now, you can keep control of which gems you programs are using by adding them to a file called `Gemfile` in your project folder (Please note the capital 'G'). 

An example `Gemfile` can look like this:
```ruby
source 'https://rubygems.org'
gem 'rack'
gem 'rspec'
gem 'mail'
```

To install those gems and their dependencies, you run the following command in your terminal:
```shell
$ bundle install
```

We'll be covering bundler and gems more extensively later on when we work with our challenges. 


#### Git
Git is a version control system that we'll be using extensively during the course. Version Control lets you track your files, changes and avoid general chaos over time. 

With Homebrew, installing Git is easy:

```shell
$ brew install git
```
And verify your installation: 

```shell
$ git --version
git version 2.4.4
```

#### Atom
At the beginning of the course (week 1 - 6) we'll be using a text editor to write code. There are many editors out there we can use. [Sublime](http://www.sublimetext.com/) is one, [TextMate](https://macromates.com/) is another, but our editor of choice for this course is [Atom](https://atom.io). It is brought to us by the good people of GitHub and comes with over 3000 extension packages - all open sourced.

*"Atom is a text editor that's modern, approachable, yet hackable to the core—a tool you can customize to do anything but also use productively without ever touching a config file."* - https://atom.io

Download and install Atom by following the instructions. Once you are done you can browse around for some packages you might find useful. Or you wait with that until you actually know what 'useful' is in the context of coding. If you want to find out more about how to install Atom packages head over to the excellent [documentation](https://atom.io/docs/v1.2.4/using-atom-atom-packages) site. 

For now, what you really want to do is to set Atom as the default editor fir Git (trust me, you'll going to need it):

```shell
$ git config --global core.editor "atom --wait"
```

#### Terminal prompt
When you work with git and version control it is a good idea to set up your prompt that displays some useful information such as:
- What branch are you currently on
- What is your current status in regard to your own GH repository (`origin`)
- What is your current status in regard to the main project repo (`upstream`)

There is a very nice modification made availiable on GitHub called [Bash Git Prompt](https://github.com/magicmonty/bash-git-prompt). You an install it using Homebrew with these steps:

```shell
$ brew install bash-git-prompt
```

Once the installation is complete, you need to open the `.bash_profile` file and add the following configuration:

```bash
# ~/.bash_profile

if [ -f "$(brew --prefix)/opt/bash-git-prompt/share/gitprompt.sh" ]; then
    source "$(brew --prefix)/opt/bash-git-prompt/share/gitprompt.sh"
fi
```

Once you save and close `.bash_profile`, go back to your terminal and reload it:

```shell
source ~/.bash_profile
```

Your terminal prompt will now show a lot more useful information that will help you in your coding. 


#### That's it!
You are all set up, you have your necessary compilers (with Xcode CLT), your package managers (RVM, Bundler and Homebrew), you have your Ruby versions installed, a version control system (Git) and you have a text editor. In your terminal, where you will be spending most of your time, you have a prompt that tells you all you need to know about where you are in your version control process.  **You are all good!**










