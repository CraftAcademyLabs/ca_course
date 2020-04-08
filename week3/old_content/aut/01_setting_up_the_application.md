---
title: "Acceptance unit test cycle"
subtitle: "Week 4"
author: [Craft Academy - Coding as a Craft]
date: Version 1.0
subject: "Rails, Ruby"
keywords: [Rails, Ruby]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

### Setting up the application

If you don't have Rails installed beforehand, it is time to install it now. Rails is a ruby gem, so you can install it as any other gem:

    $ gem install rails

Once you are done it is time to scaffold the Rails application we will be working with.

    $ rails new rails_demo --database=postgresql --skip-test

*   The `--database=postgresql` selects PostgreSQL as the database (The out-of-the-box setting is MySQL)
*   The `--skip-test` option skips configuring for the default testing tool.

    $ cd rails_demo