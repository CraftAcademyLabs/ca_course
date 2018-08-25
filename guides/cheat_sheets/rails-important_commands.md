Here is a summary of important commands that can be used as you develop your Ruby on Rails app. You must be in the root directory of your project to run any of these commands (with the exception of the rails new command). The project or application root directory is the folder containing all the subfolders described above (app, config, etc.).

<table>
<tbody>
<tr>
<th style="width: 15%;">Concept</th>
<th style="width: 25%;">Usage</th>
<th style="width: 60%;">Description</th> </tr>
<tr>
<td>Create a new app</td>
<td><strong>rails new</strong> <em>name</em></td>
<td>Create a new Ruby on Rails application with the given name here. This will give you the basic structure to immediately get started. After this command has successfully run your application is in a folder with the same name you gave the application. You have to <em>cd</em> into that folder. </td> </tr>
<tr>
<td>Start the server</td>
<td><strong>rails server</strong></td>
<td>
<p>You have to start the server in order for your application to respond to your requests. Starting the server might take some time. When it is done, you can access your application under
<a href="localhost:3000">localhost:3000</a> in the browser of your choice.</p>
<p>In order to stop the server, go to the console where it is running and press <strong>Ctrl + C</strong></p> </td> </tr>
<tr>
<td>Scaffolding</td>
<td> <strong>rails generate scaffold</strong> <em>name attribute:type</em> </td>
<td>
<p> The scaffold command magically generates all the common things needed for a new resource for you! This includes <em>controllers, models and views</em>. It also creates the following basic actions: create a new resource, edit a resource, show a resource, and delete a resource. </p>
<p>That's all the basics you need. Take this example:</p>
<p><strong>rails generate scaffold</strong> <em>product name:string price:integer</em></p>
<p>Now you can create new products, edit them, view them and delete them if you don't need them anymore. Nothing stops you from creating a full fledged web shop now ;-)</p> </td> </tr>
<tr>
<td>Run migrations</td>
<td><strong>rake db:migrate</strong></td>
<td>When you add a new migration, for example by creating a new <em>scaffold</em>, the migration has to be applied to your database. The command is used to update your database. </td> </tr>
<tr>
<td>Install dependencies</td>
<td><strong>bundle install</strong></td>
<td>If you just added a new gem to your Gemfile you should run bundle install to install it. Don't forget to restart your server afterwards! </td> </tr>
<tr>
<td>Check dependencies</td>
<td><strong>bundle check</strong></td>
<td>Checks if the dependencies listed in Gemfile are satisfied by currently installed gems </td> </tr>
<tr>
<td>Show existing routes</td>
<td><strong>rake routes</strong></td>
<td>Shows complete list of available routes in your application.</td> </tr> </tbody></table>
