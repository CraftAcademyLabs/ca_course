---
title: "Rails 5 - Active Storage"
subtitle: "Deployment with Heroku"
author: [Craft Academy - Coding as a Craft]
date: Version Beta
subject: "Elasticsearch, Rails"
keywords: [Elasticsearch, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...


### Deployment with Heroku

Heroku has an “ephemeral” hard drive, this means that you can write files to disk, but those files will not persist after the application is restarted. By default Active Storage uses a :local storage option, which uses the local file system to store any uploaded files. While file uploads that are stored with the :local option will appear to work at first, the attachments will exhibit seemingly strange behavior and eventually disappear. The files will go away when the app is deployed, or when it is automatically restarted (once every 24 hours).

Instead of storing uploaded files to disk, the best practice is to leverage a cloud file storage service such as Amazon’s S3.



#### Previews on heroku

One of the marquee features of Active Storage is the ability to use “previews” of non-image attachments. Specifically you can preview PDFs and Videos. To use this feature your application needs access to system resources that know how to work with these files. By default Rails ships with support with poppler for PDF previews, and ffmpeg for Video previews. These system dependencies are not available by default on Heroku. If you want the ability to preview these types of files with Active Support you need to add these system dependencies on your heroku instance (dyno).

Visit the [Rails 5.2 Active Storage Previews Buildpack](https://github.com/heroku/heroku-buildpack-activestorage-preview) for installation instructions