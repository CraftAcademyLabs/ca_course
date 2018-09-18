---
title: "Rails application structure"
subtitle: "Cheat sheets"
author: [Craft Academy - Coding as a Craft]
date: Version 0.1
subject: "Rails, Cheat"
keywords: [Cheat, Rails]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

Here is an overview of all the folders of a new Rails application, outlining the purpose of each folder, and describing the most important files.

<table>
<tbody>
<tr>
<th style="width: 35%;">Name</th>
<th style="width: 65%;">Description</th> </tr>
<tr>
<td>app</td>
<td>This folder contains your application. Therefore it is the <strong>most important folder</strong> in Ruby on Rails and it is worth digging into its subfolders. See the following rows. </td> </tr>
<tr>
<td>app/assets</td>
<td>Assets basically are your front-end stuff. This folder contains <em>images</em> you use on your website, <em>javascripts</em> for all your fancy front-end interaction and <em>stylesheets</em> for all your CSS making your website absolutely beautiful. </td> </tr>
<tr>
<td>app/controllers</td>
<td>The controllers subdirectory contains the controllers, which handle the requests from the users. It is often responsible for a single resource type, such as places, users or attendees. Controllers also tie together the <em>models</em> and the <em>views</em>. </td> </tr>
<tr>
<td>app/helpers</td>
<td>Helpers are used to take care of logic that is needed in the views in order to keep the views clean of logic and reuse that logic in multiple views. </td> </tr>
<tr>
<td>app/mailers</td>
<td>Functionality to send emails goes here.</td> </tr>
<tr>
<td>app/models</td>
<td>The models subdirectory holds the classes that model the business logic of our application. It is concerned with the things our application is about. Often this is data, that is also saved in the database. Examples here are a Person, or a Place class with all their typical behaviour. </td> </tr>
<tr>
<td>app/views</td>
<td>
<p>The views subdirectory contains the display templates that will be displayed to the user after a successful request. By default they are written in HTML with embedded ruby (.html.erb). The embedded ruby is used to insert data from the application. It is then converted to HTML and sent to the browser of the user. It has subdirectories for every resource of our application, e.g. places, persons. These subdirectories contain the associated view files.</p>
<p>Files starting with an underscore (<em>_</em>) are called <em>partials</em>. Those are parts of a view which are reused in other views. A common example is <em>_form.html.erb</em> which contains the basic form for a given resource. It is used in the <em>new</em> and in the <em>edit</em> view since creating something and editing something looks pretty similar. </p> </td> </tr>
<tr>
<td>config</td>
<td>This directory contains the configuration files that your application will need, including your database configuration (in <em>database.yml</em>) and the particularly important <em>routes.rb</em> which decides how web requests are handled. The <em>routes.rb</em> file matches a given URL with the <em>controller</em> which will handle the request. </td> </tr>
<tr>
<td>db</td>
<td>Contains a lot of <em>database</em> related files. Most importantly the <em>migrations</em> subdirectory, containing all your database migration files. Migrations set up your database structure, including the attributes of your models. With migrations you can add new attributes to existing models or create new models. So you could add the <em>favorite_color</em> attribute to your Person model so everyone can specify their favorite color. </td> </tr>
<tr>
<td>doc</td>
<td>Contains the documentation you create for your application. Not too important when starting out. </td> </tr>
<tr>
<td>lib</td>
<td>Short for library. Contains code you've developed that is used in your application and may be used elsewhere. For example, this might be code used to get specific information from Facebook. </td> </tr>
<tr>
<td>log</td>
<td>See all the funny stuff that is written in the console where you started the Rails server? It is written to your <em>development.log</em>. Logs contain runtime information of your application. If an error happens, it will be recorded here. </td> </tr>
<tr>
<td>public</td>
<td>Contains static files that do not contain Ruby code, such as error pages.</td> </tr>
<tr>
<td>script</td>
<td>By default contains what is executed when you type in the <em>rails</em> command. Seldom of importance to beginners. </td> </tr>
<tr>
<td>test</td>
<td>Contains the tests for your application. With tests you make sure that your application actually does what you think it does. This directory might also be called <em>spec</em>, if you are using the RSpec gem (an alternative testing framework). </td> </tr>
<tr>
<td>vendor</td>
<td>A folder for software code provided by others ("libraries"). Most often, libraries are provided as <em>ruby gems</em> and installed using the <em>Gemfile</em>. If code is not available as a ruby gem then you should put it here. This might be the case for jQuery plugins. Probably won't be used that often in the beginning. </td> </tr>
<tr>
<td>Gemfile</td>
<td>
<p>A file that specifies a list of gems that are required to run your application. Rails itself is a gem you will find listed in the Gemfile. Ruby gems are self-contained packages of code, more generally called libraries, that add functionality or features to your application. </p>
<p>If you want to add a new gem to your application, add "<strong>gem</strong> <em>gem_name</em>" to your Gemfile, optionally specifying a version number. Save the file and then run <em>bundle install</em> to install the gem. </p> </td> </tr>
<tr> 
<td>Gemfile.lock</td>
<td>This file specifies the exact versions of all gems you use. Because some gems depend on other gems, Ruby will install all you need automatically. The file also contains specific version numbers. It can be used to make sure that everyone within a team is working with the same versions of gems. The file is auto-generated. <em>Do not edit this file.</em></td> </tr> </tbody></table>
