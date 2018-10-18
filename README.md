How to Build a Reddit Clone with Ruby on Rails
In this tutorial you will get a Ruby on Rails Reddit App up and running on your local server, and will push your work into github. If you are casually browsing and are maybe interested in doing a Ruby on Rails tutorial, stop what you are doing RIGHT NOW and give this podcast a listen! It will give you the fuel you need to continue down the web developer path; it definitely fueled me.

Reading and executing tutorials is an amazing way to learn, so roll up your sleeves, take a slug of coffee, and let’s dive in!

This tutorial is loosely transposed from Mackenzie Child’s Youtube Tutorial. I’m transposing this tutorial because Mackenzie flies through the content and I wanted to slow it down a bit. I figured I’d add some value to the Ruby on Rails community and learn a little myself by transposing into blog format.

This is the second tutorial I’ve cloned from Mackenzie’s 12 Web Apps in 12 Weeks Challenge.

This style of website is called a “Link Aggregator” and is similar to Reddit and Hacker News.

The first thing I’m going to do is create a github repository for my work:



Next, I’m going to jump into my command line and create the bones of the application:

ls
Which lists the files in the current directory:


cd Desktop
Which “changes directory”.

Then I’ll list the files in Desktop again with:

ls
And then cd (change directory) into my Project folder:

cd Projects
Type ls again and I see my other Rails projects

ls

I’m right where I want to be, so I’m going to go ahead and create the bones of the application with:

rails new redditclone

cd into your new app, and then let’s make sure everything is working correctly:

cd redditclone
and then navigate to http://localhost:3000/.

Your page should look like this:


We need to start the rails server before anything will happen at http://localhost:3000/:

rails server
Refresh the page and:


Great. Everything is working properly. Next I’m going to commit these changes to me github repository for this project:

git init
git commit -m "first commit"
git remote add origin https://github.com/username/reddit-clone.git
git push -u origin master
Check github:


Everything looks good.

Next I’m going to open up the project file in my text editor:



This will get us to 3:20 / 1:08:06 of Mackenzie’s tutorial. In case you get stuck, it’s probably a good idea to check out his video to see what I may have bungled. I’ll continue to pepper in time-stamped video links for you as we go along.

Requirements for our site:

Users need to be able to sign-up, sign-in and sign-out
Signed-in users will need to be able to submit a link with a title and a URL
Users will be able to vote up or down on the link submissions
Users will be able to comment on link submissions
We will begin with link submissions

First, we are going to create a new branch in git:

git checkout -b link_scaffold
And then we will generate a scaffold for the link submission:

rails g scaffold link title:string url:string
Which gives us the following results in our terminal:


Migrate the database we created with the scaffold:

rake db:migrate
And then let’s run the rails server to check out our work:

rails server
Navigate to http://localhost:3000/links :


Great! It’s all working.

I’m going to add a couple of links and make sure everything is working.



Excellent. Everything is functioning.

Next we will commit our changes and merge our git branch into the git master branch:

Kill the server: ctrl + c. Then execute each git command individually in your terminal. Write it out rather than copy/paste; you’ll want to commit these commands to memory.

git status
git add .
git commit -m "generated link scaffold"
git checkout master
git merge link_scaffold
git push
Next, we will create users

This will give us the ability to signup, sign-in and sign-out. We will be using the devise gem to help us add user authentication into our app.

First we are going to create a new branch in our github repository:

git checkout -b add_users
“Switched to a new branch ‘add_users’

Next, we will actually add the devise gem to our gemfile. Navigate to the Ruby devise gem page and copy the gemfile:

gem 'devise', '~> 4.4', '>= 4.4.1'
Open the gem file in your text editor, and add the above line of code into the gemfile. See lines 8 and 9:


Save the file.

In the command line:

bundle install
Next, we’ll finish installing devise:

rails g devise:install
You’ll see the following in your command line:


Let’s complete setup.

First, add the following to config/environments/development.rb:

config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
Save the file.

Second, add a root to your routes.rb file. I’m going to make the root the links index page:

Before:

Rails.application.routes.draw do
  resources :links
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end
After:

Rails.application.routes.draw do
  resources :links
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
root:'links#index'
end
Check to see if your routes.rb update worked:

rails server
Got to http://localhost:3000/


Great! It worked.

Third piece of the devise gem install: Ensure you have flash messages in app/views/layouts/application.html.erb.

Mackenzie improvises a bit here, and uses some code that will work with the bootstrap code that we will add later. We’ll follow along with him.

Add the following to app/views/layouts/application.html.erb:

<% flash.each do |name, msg| %>
    <%= content_tag(:div, msg, class: "alert alert-#{name}") %>
   <% end %>
Before:

<!DOCTYPE html>
<html>
  <head>
    <title>Redditclone</title>
    <%= csrf_meta_tags %>
<%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>
<body>
<%= yield %>
  </body>
</html>
After:

<!DOCTYPE html>
<html>
  <head>
    <title>Redditclone</title>
    <%= csrf_meta_tags %>
<%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>
<body>
   <% flash.each do |name, msg| %>
    <%= content_tag(:div, msg, class: "alert alert-#{name}") %>
   <% end %>
<%= yield %>
  </body>
</html>
Next, we’re going kill the server (ctrl +c), and then run:

rails g devise:views

Great. Now we can generate a user.

rails g devise User
Then, migrate the database:

rake db:migrate

Now we can create a user on our web app.

Restart the server

rails server
And head to http://localhost:3000/users/sign_up

If your page looks like this, your code is working:


I’m going to sign up; looks like it worked!


Next, we are going to get into rails console and take a look at the database:

(Video link here)

Kill the server. Then:

rails c
Then:

User.count
This will tell us how many users we have on the web app. It should be “1”.

Then:

@user = User.first
And you should see the email you just entered in the terminal. I see:


Control + d will exit the rails console.

FYI, if you like to keep a clean workspace, you can always clear the terminal with:

clear
Now we are going to commit our changes:

git status
then

git add .
then

git commit -m "Added devise gem and User model"
Now we can sign in and out, but we need to update our view files so that we have some accessible links on the home page. We don’t want users to have to know to navigate to http://localhost:3000/sign_up do we?

(video link)

Now, in our app/views/layouts/application.html.erb file, we are going to add a conditional statement to serve different links depending on whether the user is signed in or not:

<% if user_signed_in? %>
    <ul>
     <li><%= link_to 'Submit link', new_link_path %></li>
     <li><%= link_to 'Account', edit_user_registration_path %></li>
     <li><%= link_to 'Sign out', destroy_user_session_path, :method => :delete %></li>
    </ul>
   <% else %>
    <ul>
     <li><%= link_to 'Sign up', new_user_registration_path%></li>
     <li><%= link_to 'Sign in', new_user_session_path %></li>
    </ul>
   <% end %>
The whole code block will look like this before:

<!DOCTYPE html>
<html>
  <head>
    <title>Redditclone</title>
    <%= csrf_meta_tags %>
<%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>
<body>
   <% flash.each do |name, msg| %>
    <%= content_tag(:div, msg, class: "alert alert-#{name}") %>
   <% end %>
<%= yield %>
  </body>
</html>
And this, after:

<!DOCTYPE html>
<html>
  <head>
    <title>Redditclone</title>
    <%= csrf_meta_tags %>
<%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>
<body>
   <% flash.each do |name, msg| %>
    <%= content_tag(:div, msg, class: "alert alert-#{name}") %>
   <% end %>
<% if user_signed_in? %>
    <ul>
     <li><%= link_to 'Submit link', new_link_path %></li>
     <li><%= link_to 'Account', edit_user_registration_path %></li>
     <li><%= link_to 'Sign out', destroy_user_session_path, :method => :delete %></li>
    </ul>
   <% else %>
    <ul>
     <li><%= link_to 'Sign up', new_user_registration_path%></li>
     <li><%= link_to 'Sign in', new_user_session_path %></li>
    </ul>
   <% end %>
<%= yield %>
  </body>
</html>
Don’t forget to save your file.

Start the server:

rails server
And head to http://localhost:3000/ to make sure everything is working:


Yes! Everything is working.

Go ahead and sign out and then sign back in to make sure EVERYTHING is working.

Now, we are going to create an association between the user and the links to make sure that unregistered users can’t navigate to the url for submit link and submit a link. Because as it stands, a non-registered user could do that; they’d just need to know the url path.

(video link)

Head to the user model at app/models/user.rb and add

has_many :links
Before:

class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
end
After:

class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  has_many :links
end
And then we will add an association to the link.rb file:

belongs_to :user
Before:

class Link < ApplicationRecord
end
After:

class Link < ApplicationRecord
 belongs_to :user
end
Then we are going to get back into the rails console to inspect our work.

rails c
We are going to ask the rails console what the first link is:

@link = Link.first

Then let’s ask the console what user submitted this link:

@link.user
The console returns => nil

Without this association that we just made between users and links, we would have been shown an error message for the query “@link.user”

Don’t mistake “nil” for an error. This isn’t technically an error, it just means that we haven’t added a user column to our database. Let’s do that now.

Exit the console with control + d.

We are going to run a migration to add users to the links database:

rails g migration add_user_id_to_links user_id:integer:index
then run:

rake db:migrate
Output:


Now we are going to get back into the rails console to confirm our work:

rails c
First we do:

Link.connection
And we get a bunch of gobbleygook, and then we do:

Link
And see: => Link(id: integer, title: string, url: string, created_at: datetime, updated_at: datetime, user_id: integer)

The id integer is there! Success.

Exit the console; control + d

Annnnd it’s time for a commit.

git status
then

git add .
then

git commit -m "add association between link and user"
Great.

Next, we are going to update our link controller so that when a user submits a link, their user id gets assigned to that link.

(video link)

We are going to update the new and create methods inside the app/controller/links_controller.rb

New method before:

def new
    @link = Link.new
  end
New method after:

def new
    @link = current_user.links.build
  end
Create method before:

def create
    @link = Link.new(link_params)
respond_to do |format|
      if @link.save
        format.html { redirect_to @link, notice: 'Link was successfully created.' }
        format.json { render :show, status: :created, location: @link }
      else
        format.html { render :new }
        format.json { render json: @link.errors, status: :unprocessable_entity }
      end
    end
  end
Create method after:

def create
    @link = current_user.links.build(link_params)
respond_to do |format|
      if @link.save
        format.html { redirect_to @link, notice: 'Link was successfully created.' }
        format.json { render :show, status: :created, location: @link }
      else
        format.html { render :new }
        format.json { render json: @link.errors, status: :unprocessable_entity }
      end
    end
  end
Save the file!

Let’s confirm that this worked. Add a new link, and….


Huzzah! Let’s get back into the rails console to check our work.

rails c
Then:

@link = Link.last

Great! user_id: 1 submitted this last link. That was me (you). Now let’s check in on user_id: 1

@link.user

Cool. Everything is working.

Now we will add some authentication to our control to make sure users stay within their lanes.

(video link)

First, we will add a before filter to our links_controller.rb:

before_filter :authenticate_user!, except: [:index, :show]
We’ll add it right there at the top. Before:

class LinksController < ApplicationController
  before_action :set_link, only: [:show, :edit, :update, :destroy]
After:

class LinksController < ApplicationController
  before_action :set_link, only: [:show, :edit, :update, :destroy]
  before_filter :authenticate_user!, except: [:index, :show]
To test this, let’s log out and check out the site and see if we can delete a link.


ERRROROROROROROROR.

After a bit of research I learned that before_filters have been depreciated. Link here. We need to update our before_filter to a before_action.

before_action :authenticate_user!, except: [:index, :show]
I also checked Mackenzie’s github repository to see what his code looked like for this project. Sure enough, his has been changed to a before_action instead of a before_filter.

Moving on. Links are still showing even if we aren’t signed in. Let’s change that.

(video link)

Go to app/views/links/index.html.erb. We are going to wrap the ‘Edit’ path in an if block so that a user cannot see the edit path IF they are not signed in.

That block of code will look like this:

<% if link.user == current_user %>
        <td><%= link_to 'Edit', edit_link_path(link) %></td>
        <td><%= link_to 'Destroy', link, method: :delete, data: { confirm: 'Are you sure?' } %></td>
        <% end %>
Whole page before:

<p id="notice"><%= notice %></p>
<h1>Links</h1>
<table>
  <thead>
    <tr>
      <th>Title</th>
      <th>Url</th>
      <th colspan="3"></th>
    </tr>
  </thead>
<tbody>
    <% @links.each do |link| %>
      <tr>
        <td><%= link.title %></td>
        <td><%= link.url %></td>
        <td><%= link_to 'Show', link %></td>
        <td><%= link_to 'Edit', edit_link_path(link) %></td>
        <td><%= link_to 'Destroy', link, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>
<br>
<%= link_to 'New Link', new_link_path %>
Whole page after:

<p id="notice"><%= notice %></p>
<h1>Links</h1>
<table>
  <thead>
    <tr>
      <th>Title</th>
      <th>Url</th>
      <th colspan="3"></th>
    </tr>
  </thead>
<tbody>
    <% @links.each do |link| %>
      <tr>
        <td><%= link.title %></td>
        <td><%= link.url %></td>
        <td><%= link_to 'Show', link %></td>
<% if link.user == current_user %>
        <td><%= link_to 'Edit', edit_link_path(link) %></td>
        <td><%= link_to 'Destroy', link, method: :delete, data: { confirm: 'Are you sure?' } %></td>
        <% end %>

      </tr>
    <% end %>
  </tbody>
</table>
<br>
<%= link_to 'New Link', new_link_path %>
Alright! Let’s start out server and test out our change:

rails server
Got to: http://localhost:3000/ and sign out. Refresh the page.


Hmmm. It looks like this worked for the third link, but not for the first two. Do you know why?

We told our app to show the Edit and Destroy buttons only if the link.user == current user. But, wecreated the first two links before we added a user column to the database. So, the code we wrote won’t work for those first two links.

Let’s sign in and create a new link just to test out our theory:


Yay. You can see that we cannot edit or destroy those first two links because we did not create them (technically, no one did, according to the database). We DID create the third link, however, and we are allowed to destroy it.

Let’s get into the console and confirm our theory.

rails c
then:

@link = Link.first
then:

@link = Link.second
See! You see the user_id for both of these links is set to “nil”.

We can update the database to show that the first user did indeed create these links.

First:

@link = Link.first
then

@link.user = User.first
then

@link.save
then

@link = Link.second
then

@link.user = User.first
then

@link.save
Ok, restart your server and refresh your page.

Huzzah!


Next, we will make sure you are signed in before you can submit a new link. Right now, you can submit a new link even if you are signed out. See?


(video link)

Under: app/views/links/index.html.erb just simply delete the following at the bottom of the page:

<%= link_to 'New Link', new_link_path %>
Now it’s time for a git commit!

git status
git add .
git commit -m "authorization on links"
git checkout master
git merge add_users
git push
Lovely. Now it’s time to add bootstrap and start styling a bit.

(video link)

git checkout -b add_bootstrap
Find the bootstrap gem on Google. I found it here. Copy the gem and add to your Gemfile:

gem 'bootstrap-sass', '~> 3.3', '>= 3.3.7'
Save. Run a bundle install:

bundle install
Bundle complete! Still some work to do before we can use bootstrap. Head over to the bootstrap sass github page and find the installation directions.

First thing we’ll do here is rename the app/assets/stylesheets/application.css page to app/assets/stylesheets/application.css.scss.


Import this into your new .css.scss page:

// "bootstrap-sprockets" must be imported before "bootstrap" and "bootstrap/variables"
@import "bootstrap-sprockets";
@import "bootstrap";
We also need to “//= require bootstrap-sprockets” inside our app/assets/javascripts/application.js page:

//= require bootstrap-sprockets
Save. Next, delete the scaffold.scss file. It will override your bootstrap stylings and we don’t want that.

Next, let’s head over to app/views/layouts/application.html.erb and start adding some stylings:

(video link)

I hope you don’t mind that we’re just copy/pasting Mackenzie’s stylings. This tutorial is mainly geared towards learning Ruby on Rails, not CSS. So we’re going to cut some corners here. I’m going to head to Mackenzie’s github page for this project, find the application.html.erb layout page, and copy / paste the code.

Before:

<!DOCTYPE html>
<html>
  <head>
    <title>Redditclone</title>
    <%= csrf_meta_tags %>
<%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>
<body>
   <% flash.each do |name, msg| %>
    <%= content_tag(:div, msg, class: "alert alert-#{name}") %>
   <% end %>
<% if user_signed_in? %>
    <ul>
     <li><%= link_to 'Submit link', new_link_path %></li>
     <li><%= link_to 'Account', edit_user_registration_path %></li>
     <li><%= link_to 'Sign out', destroy_user_session_path, :method => :delete %></li>
    </ul>
   <% else %>
    <ul>
     <li><%= link_to 'Sign up', new_user_registration_path%></li>
     <li><%= link_to 'Sign in', new_user_session_path %></li>
    </ul>
   <% end %>
<%= yield %>
  </body>
</html>
After:

<!DOCTYPE html>
<html>
<head>
  <title>Raddit</title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>
</head>
<body>
  <header class="navbar navbar-default" role="navigation">
    <div class="navbar-inner">
      <div class="container">
        <div id="logo" class="navbar-brand"><%= link_to "Raddit", root_path %></div>
        <nav class="collapse navbar-collapse navbar-ex1-collapse">
          <% if user_signed_in? -%>
            <ul class="nav navbar-nav navbar-right">
              <li><%= link_to 'Submit link', new_link_path %></li>
              <li><%= link_to 'Account', edit_user_registration_path %></li>
              <li><%= link_to 'Sign out', destroy_user_session_path, :method => :delete %></li>
            </ul>
          <% else -%>
            <ul class="nav navbar-nav pull-right">
              <li><%= link_to 'Sign up', new_user_registration_path %></li>
              <li><%= link_to 'Sign in', new_user_session_path %></li>
            </ul>
          <% end -%>
        </nav>
      </div>
    </div>
  </header>
<div id="main_content" class="container">
    <% flash.each do |name, msg| %>
      <%= content_tag(:div, msg, class: "alert alert-info") %>
    <% end %>
<div id="content" class="col-md-9 center-block">
        <%= yield %>
      </div>
    </div>
  </div>
</body>
</html>
Then

rails server
And let’s see what we get at http://localhost:3000/


Nice. Stealing like an artist. Ok, moving on.

Now let’s add some styling to the application.css.scss file. Find the file on the github repo for this project, and copy / paste the content into your own application.css.scss file:

Before:

// "bootstrap-sprockets" must be imported before "bootstrap" and "bootstrap/variables"
@import "bootstrap-sprockets";
@import "bootstrap";
After:

// "bootstrap-sprockets" must be imported before "bootstrap" and "bootstrap/variables"
@import "bootstrap-sprockets";
@import "bootstrap";
#logo {
 font-size: 26px;
 font-weight: 700;
 text-transform: uppercase;
 letter-spacing: -1px;
 padding: 15px 0;
 a {
  color: #2F363E;
 }
}
#main_content {
 #content {
  float: none;
 }
 padding-bottom: 100px;
 .link {
  padding: 2em 1em;
  border-bottom: 1px solid #e9e9e9;
  .title {
a {
    color: #FF4500;
   }
  }
 }
 .comments_title {
  margin-top: 2em;
 }
 #comments {
  .comment {
   padding: 1em 0;
   border-top: 1px solid #E9E9E9;
   .lead {
    margin-bottom: 0;
   }
  }
 }
}
Refresh your localhost page annddddd:


Progress. Next, we’ll attack the index.html.erb file.

(video link)

We are going to delete EVERYTHING. And add in the following:

<% @links.each do |link| %>
  <div class="link row clearfix">
    <h2>
        <%= link_to link.title, link %><br>
        <small class="author">Submitted <%= time_ago_in_words(link.created_at) %> by <%= link.user.email %></small>
    </h2>
  </div>
  <% end %>
Please forgive me for glossing over this stuff and not explaining the “how” and “why” behind this stuff. I’m learning as I go and my approach (for now) is to repeat, repeat, repeat, repeat, repeat, repeat.

That being said, avoid copy / pasting and write this stuff out yourself. Try to translate the code, and predict what it will do as you write.

Save your code. Refresh your page. Walah!


Next, let’s tackle the show.html.erb page. Delete everything in there and write:

<div class="page-header">
 <h1><a href="<%= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%= @link.user.email %></small></h1>
</div>
<div class="btn-group">
 <%= link_to 'Visit URL', @link.url, class: "btn btn-primary" %>
</div>
<% if @link.user == current_user %>
 <div class="btn-group">
  <%= link_to 'Edit', edit_link_path(@link), class: "btn btn-default" %>
  <%= link_to 'Destroy', @link, method: :delete, data: { confirm: 'Are you sure?' }, class: "btn btn-default" %>
 </div>
<% end %>
Click on one of your links and check out your new stylings:


Cool. Now let’s tackle the style of the forms

(video link)

Before:

<%= form_with(model: link, local: true) do |form| %>
  <% if link.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(link.errors.count, "error") %> prohibited this link from being saved:</h2>
<ul>
      <% link.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
<div class="field">
    <%= form.label :title %>
    <%= form.text_field :title, id: :link_title %>
  </div>
<div class="field">
    <%= form.label :url %>
    <%= form.text_field :url, id: :link_url %>
  </div>
<div class="actions">
    <%= form.submit %>
  </div>
<% end %>
After:

<%= form_with(model: link, local: true) do |form| %>
  <% if link.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(link.errors.count, "error") %> prohibited this link from being saved:</h2>
<ul>
      <% link.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
<div class="form-group">
    <%= form.label :title %><br>
    <%= form.text_field :title, class: "form-control" %>
  </div>
<div class="form-group">
    <%= form.label :url %><br>
    <%= form.text_field :url, class: "form-control" %>
  </div>
  <br>
  <div class="actions">
    <%= form.submit "Submit", class: "btn btn-kg btn-primary" %>
  </div>
<% end %>
Guess what this update does.

Refresh your page, and….


Next, we are going to update the look of the account page. Navigate to app/views/devise/registrations/edit.html.erb

(video link)

Before:

<h2>Edit <%= resource_name.to_s.humanize %></h2>
<%= form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
  <%= devise_error_messages! %>
<div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, autocomplete: "email" %>
  </div>
<% if devise_mapping.confirmable? && resource.pending_reconfirmation? %>
    <div>Currently waiting confirmation for: <%= resource.unconfirmed_email %></div>
  <% end %>
<div class="field">
    <%= f.label :password %> <i>(leave blank if you don't want to change it)</i><br />
    <%= f.password_field :password, autocomplete: "off" %>
    <% if @minimum_password_length %>
      <br />
      <em><%= @minimum_password_length %> characters minimum</em>
    <% end %>
  </div>
<div class="field">
    <%= f.label :password_confirmation %><br />
    <%= f.password_field :password_confirmation, autocomplete: "off" %>
  </div>
<div class="field">
    <%= f.label :current_password %> <i>(we need your current password to confirm your changes)</i><br />
    <%= f.password_field :current_password, autocomplete: "off" %>
  </div>
<div class="actions">
    <%= f.submit "Update" %>
  </div>
<% end %>
<h3>Cancel my account</h3>
<p>Unhappy? <%= button_to "Cancel my account", registration_path(resource_name), data: { confirm: "Are you sure?" }, method: :delete %></p>
<%= link_to "Back", :back %>
After:

<h2>Edit <%= resource_name.to_s.humanize %></h2>
<%= form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
  <%= devise_error_messages! %>
<div class="panel panel-default">
    <div class="panel-body">
<div class="form-inputs">
<div class="form-group">
        <%= f.label :email %>
        <%= f.email_field :email, class: "form-control", :autofocus => true %>
      </div>
<div class="form-group">
        <%= f.label :password %> <i>(leave blank if you don't want to change it)</i>
        <%= f.password_field :password, class: "form-control", :autocomplete => "off" %>
      </div>
<div class="form-group">
        <%= f.label :current_password %> <i>(we need your current password to confirm your changes)</i>
        <%= f.password_field :current_password, class: "form-control" %>
      </div>
</div>
<div class="form-group">
        <%= f.submit "Update", class: "btn btn-primary" %>
      </div>
<% end %>
  </div>
  <div class="panel-footer">
<h3>Cancel my account</h3>
<p>Unhappy? <%= button_to "Cancel my account", registration_path(resource_name), data: { confirm: "Are you sure?" }, method: :delete, class: "btn btn-default" %></p>
</div>
Looking good!


Then, navigate to app/views/devise/registrations/new.html.erb and pull the same stunt.

Before:

<h2>Sign up</h2>
<%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
  <%= devise_error_messages! %>
<div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, autocomplete: "email" %>
  </div>
<div class="field">
    <%= f.label :password %>
    <% if @minimum_password_length %>
    <em>(<%= @minimum_password_length %> characters minimum)</em>
    <% end %><br />
    <%= f.password_field :password, autocomplete: "off" %>
  </div>
<div class="field">
    <%= f.label :password_confirmation %><br />
    <%= f.password_field :password_confirmation, autocomplete: "off" %>
  </div>
<div class="actions">
    <%= f.submit "Sign up" %>
  </div>
<% end %>
<%= render "devise/shared/links" %>
After:

<h2>Sign up</h2>
<%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
  <%= devise_error_messages! %>
<div class="form-group">
      <%= f.label :email %>
      <%= f.email_field :email, autofocus: true, class: "form-control", required: true %>
    </div>
<div class="form-group">
      <%= f.label :password %>
      <%= f.password_field :password, class: "form-control", required: true %>
    </div>
<div class="form-group">
      <%= f.label :password_confirmation %>
      <%= f.password_field :password_confirmation, class: "form-control", required: true %>
    </div>
<div class="form-group">
      <%= f.submit "Sign up", class: "btn btn-lg btn-primary" %>
    </div>
<% end %>
<%= render "devise/shared/links" %>
SAVE!

To update the sign-in page, we need to navigate to app/views/devise/sessions/new.html.erb and update the code:

Before:

<h2>Log in</h2>
<%= form_for(resource, as: resource_name, url: session_path(resource_name)) do |f| %>
  <div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, autocomplete: "email" %>
  </div>
<div class="field">
    <%= f.label :password %><br />
    <%= f.password_field :password, autocomplete: "off" %>
  </div>
<% if devise_mapping.rememberable? -%>
    <div class="field">
      <%= f.check_box :remember_me %>
      <%= f.label :remember_me %>
    </div>
  <% end -%>
<div class="actions">
    <%= f.submit "Log in" %>
  </div>
<% end %>
<%= render "devise/shared/links" %>
After:

<h2>Sign in</h2>
<%= form_for(resource, as: resource_name, url: session_path(resource_name)) do |f| %>
<div class="form-group">
    <%= f.label :email %>
    <%= f.email_field :email, autofocus: true, class: "form-control", required: false %>
  </div>
<div class="form-group">
    <%= f.label :password %>
    <%= f.password_field :password, class: "form-control", required: false %>
  </div>
<div class="checkbox">
    <label>
    <%= f.check_box :remember_me, required: false, as: :boolean if devise_mapping.rememberable? %> Remember Me
    </label>
  </div>
<div class="form-group">
    <%= f.submit "Sign In", class: "btn btn-primary" %>
  </div>
<% end %>
<%= render "devise/shared/links" %>
If you are using Chrome, use the incognito mode to check out your new Sign in page. Otherwise, sign out and then sign back in to see your changes in action.

Ok, time for a commit! Kill the server, and:

git status
git add .
git commit -m "add structure and styling"
git checkout master
git merge add_bootstrap
git push
Wonderful. Now we are going to tackle the voting function. FUN!

(video link)

Search for the github page for this gem. I’ve conveniently located it for you here.

We’ll add the following to our gemfile and then run a bundle install.

gem 'acts_as_votable', '~> 0.11.1'
then

bundle install
Cool. Now we just need to follow the installation instructions on the github page. Let’s create a table to store the votes in:

rails generate acts_as_votable:migration
then

rake db:migrate
Next, we need to add a line to our app/models/link.rb file:

Before:

class Link < ApplicationRecord
 belongs_to :user
end
After:

class Link < ApplicationRecord
 belongs_to :user
 acts_as_votable
end
Now let’s jump into the rails console to add our first link like and make sure this is all working:

rails c
Then

@link = Link.first
then

@user = User.first
then

@link.liked_by @user
then

@link.votes_for.size
then

@link.save
Your output should look something like this:


Great. It’s working.

Now we need to get this data inside of our views so that we can see the votes in our HTML!

(video link)

First, we’ll go into our routes file and add some routes.

Before:

Rails.application.routes.draw do
  devise_for :users
  resources :links
root to: 'links#index'
end
After:

Rails.application.routes.draw do
  devise_for :users
  resources :links do
   member do
    put "like", to: "links#upvote"
    put "dislike", to: "links#downvote"
   end
  end
root to: 'links#index'
end
Then, inside of our links controller (app/controllers/links_controllers.rb), we need to create an upvote and downvote method.

First the upvote method:

def upvote
    @link = Link.find(params[:id])
    @link.upvote_by current_user
    redirect_back fallback_location: root_path
  end
Then the downvote method:

def downvote
    @link = Link.find(params[:id])
    @link.downvote_by current_user
    redirect_back fallback_location: root_path
  end
NOTE: Mackenzie wrote his app three years ago, so some of the code is no out-of-date. We need to use “redirect_back fallback_location: root_path” instead of “redirect_to :back” as specified in the video.

Save. Now we will add the ability to upvote or downvote inside of our views.

Before:

<% @links.each do |link| %>
  <div class="link row clearfix">
    <h2>
        <%= link_to link.title, link %><br>
        <small class="author">Submitted <%= time_ago_in_words(link.created_at) %> by <%= link.user.email %></small>
    </h2>
  </div>
  <% end %>
After:

<% @links.each do |link| %>
  <div class="link row clearfix">
    <h2>
        <%= link_to link.title, link %><br>
        <small class="author">Submitted <%= time_ago_in_words(link.created_at) %> by <%= link.user.email %></small>
    </h2>
<div class="btn-group">
      <a class="btn btn-default btn-sm" href="<%= link.url %>">Visit Link</a>
      <%= link_to like_link_path(link), method: :put, class: "btn btn-default btn-sm" do %>
      <span class="glyphicon glyphicon-chevron-up"></span>
      Upvote
      <%= link.get_upvotes.size %>
      <% end %>
      <%= link_to dislike_link_path(link), method: :put, class: "btn btn-default btn-sm" do %>
      <span class="glyphicon glyphicon-chevron-down"></span>
      Downvote
      <%= link.get_downvotes.size %>
       <% end %>
    </div>
   </div>
  <% end %>
Make sure all of your files have been saved before you refresh and test your code.

Refresh and test!


Niiiiiiiiiiiice. Go ahead and give those links some votes to make sure everything is working correctly.

Niiiiiice. Now, we are going to add the votes to the show page as well.

In app/views/links/show.html.erb, we will add some code.

Before:

<div class="page-header">
 <h1><a href="<%= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%= @link.user.email %></small></h1>
</div>
<div class="btn-group">
 <%= link_to 'Visit URL', @link.url, class: "btn btn-primary" %>
</div>
<% if @link.user == current_user %>
 <div class="btn-group">
  <%= link_to 'Edit', edit_link_path(@link), class: "btn btn-default" %>
  <%= link_to 'Destroy', @link, method: :delete, data: { confirm: 'Are you sure?' }, class: "btn btn-default" %>
 </div>
<% end %>
After:

<div class="page-header">
 <h1><a href="<%= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%= @link.user.email %></small></h1>
</div>
<div class="btn-group">
 <%= link_to 'Visit URL', @link.url, class: "btn btn-primary" %>
</div>
<% if @link.user == current_user %>
 <div class="btn-group">
  <%= link_to 'Edit', edit_link_path(@link), class: "btn btn-default" %>
  <%= link_to 'Destroy', @link, method: :delete, data: { confirm: 'Are you sure?' }, class: "btn btn-default" %>
 </div>
<% end %>
<div class="btn-group pull-right">
 <%= link_to like_link_path(@link), method: :put, class: "btn btn-default btn-sm" do %>
  <span class="glyphicon glyphicon-chevron-up"></span>
  Upvote
  <%= @link.get_upvotes.size %>
 <% end %>
 <%= link_to dislike_link_path(@link), method: :put, class: "btn btn-default btn-sm" do %>
  <span class="glyphicon glyphicon-chevron-down"></span>
  Downvote
  <%= @link.get_downvotes.size %>
 <% end %>
</div>
Great. Save. Refresh. Test.


Niiiiiice. Let’s git it on! Lol.

git status
git add .
git commit -m "added and set up act_as_votable
git push
ONE MORE FEATURE! Let’s add the ability to comment on a link.

(video link)

I imagine there is a gem out there that does this, but we’re going to scaffold this ourselves instead.

rails g scaffold Comment link_id:integer:index body:text user:references

Niiice. Ok, next:

rake db:migrate
And refresh your server to make sure everything is running ok.

It’s not. I jumped the gun and pulled the trigger before the tutorial had typed out everything. BUT IT’S OK. We can fix this.

Go into app/assets/stylesheets/scaffolds.scss and delete all the code. Save. Refresh. And walah! Back to normal.

For the comment function, we are going to use a gem called “simple form”. I’ve placed the link right →here← for your convenience.

By now you’re a gem pro. We just need to copy paste this into our gemfile:

gem 'simple_form'
Save, bundle install.

bundle install
Then head over to our models and update the comment.rb file to:

class Comment < ApplicationRecord
  belongs_to :user
  belongs_to :link
end
Also, we need to update the link.rb file to:

class Link < ApplicationRecord
 belongs_to :user
 acts_as_votable
 has_many :comments
end
Save those files.

Next, we will add a resource to our routes.rb file.

Before:

Rails.application.routes.draw do
  resources :comments
  devise_for :users
  resources :links do
   member do
    put "like", to: "links#upvote"
    put "dislike", to: "links#downvote"
   end
  end
root to: 'links#index'
end
After:

Rails.application.routes.draw do
  resources :comments
  devise_for :users
  resources :links do
   member do
    put "like", to: "links#upvote"
    put "dislike", to: "links#downvote"
   end
   resources :comments
  end
root to: 'links#index'
end
Now we need to update the create method in our comments controller.

(video link)

The whole comments_controller.rb file will look like this:

class CommentsController < ApplicationController
  before_action :set_comment, only: [:show, :edit, :update, :destroy]
# GET /comments
  # GET /comments.json
  def index
    @comments = Comment.all
  end
# GET /comments/1
  # GET /comments/1.json
  #def show
  #end
# GET /comments/new
  def new
    @comment = Comment.new
  end
# GET /comments/1/edit
  #def edit
  #end
# POST /comments
  # POST /comments.json
  def create
    @link = Link.find(params[:link_id])
    @comment = @link.comments.new(comment_params)
    @comment.user = current_user
respond_to do |format|
      if @comment.save
        format.html { redirect_to @link, notice: 'Comment was successfully created.' }
        format.json { render json: @comment, status: :created, location: @comment }
      else
        format.html { render action: "new" }
        format.json { redner json: @comment.errors, status: :unprocessable_entity }
      end
    end
  end
# PATCH/PUT /comments/1
  # PATCH/PUT /comments/1.json

  #def update
    #respond_to do |format|
      #if @comment.update(comment_params)
        #format.html { redirect_to @comment, notice: 'Comment was successfully updated.' }
        #format.json { render :show, status: :ok, location: @comment }
      #else
        #format.html { render :edit }
        #format.json { render json: @comment.errors, status: :unprocessable_entity }
      #end
    #end
  #end
# DELETE /comments/1
  # DELETE /comments/1.json
  def destroy
    @comment.destroy
    respond_to do |format|
      format.html { redirect_back fallback_location: root_path, notice: 'Comment was successfully destroyed.' }
      format.json { head :no_content }
    end
  end
private
    # Use callbacks to share common setup or constraints between actions.
    def set_comment
      @comment = Comment.find(params[:id])
    end
# Never trust parameters from the scary internet, only allow the white list through.
    def comment_params
      params.require(:comment).permit(:link_id, :body, :user_id)
    end
end
Next, we need to add a form to our show page.

(video link)

Under app/views/links/show.html.erb, at the bottom of the file, add:

<h3 class="comments_title">
 <%= @link.comments.count %> comments
</h3>
<div id="comments">
 <%= render :partial => @link.comments %>
</div>
<%= simple_form_for [@link, Comment.new] do |f| %>
 <div class="field">
  <%= f.text_area :body, class: "form-control" %>
 </div>
 <br>
 <%= f.submit "Add Comment", class: "btn btn-primary" %>
<% end %>
Now we need to create the partial that we are rendering.

(video link)

We need to save a new file “_comment.html.erb” under app/views/comments.

<%= div_for(comment) do %>
 <div class="comments_wrapper clearfix">
  <div class="pull-left">
   <p class="lead"><%= comment.body %></p>
   <p><small>Submitted <strong><%= time_ago_in_words(comment.created_at) %> ago</strong> by <%= comment.user.email %></small></p>
  </div>
<div class="btn-group pull-right">
   <% if comment.user == current_user %>
    <%= link_to 'Destroy', comment, method: :delete, data: { confirm: 'Are you sure?' }, class: "btn btn-sm btn-default" %>
    <% end %>
  </div>
 </div>
<% end %>
For this file to work, we need to add a new gem. Ruby no longer recognizes the div_for command, but we can make it work if we add the following gem to our gemfile:

#so that we can use the 'div_for' method
gem 'record_tag_helper', '~> 1.0'
then

bundle install
then

rails s
And refresh your page. http://localhost:3000/links/1

Haha! It worked. HUZZZZZZAAAAAAHHHHHH.

Now, let’s commit to git

git status
git add .
git commit -m "added comments"
git push
Nice.

Ok, one last thing. We’re going to add a username so the it says “Submitted about 8 hours… oops… typo! Need to fix that. It should read 8 hours ago by davideallen7@gmail.com


We just need to go into the links/index.html.erb file and update the <h2> class heading to:

<h2>
        <%= link_to link.title, link %><br>
        <small class="author">Submitted <%= time_ago_in_words(link.created_at) %> ago by <%= link.user.email %></small>
    </h2>
Back to the username.

rails g migration add_name_to_users name:string
then

rake db:migrate
then we’ll add an input into our form field to accept a username upon sign-up.

Head over to app/views/devise/registration/edit.html.erb, and add:

<div class="form-group">
        <%= f.label :name %>
        <%= f.text_field :name, class: "form-control", :autofocus => true %>
      </div>
And then get into the application controller and update to:

class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  before_action :configure_permitted_parameters, if: :devise_controller?
protected
def configure_permitted_parameters
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name])
    devise_parameter_sanitizer.permit(:account_update, keys: [:name])
  end
end
Then update the <h2> code block in your index.html.erb file from:

<h2>
        <%= link_to link.title, link %><br>
        <small class="author">Submitted <%= time_ago_in_words(link.created_at) %> ago by <%= link.user.email %></small>
    </h2>
to:

<h2>
        <%= link_to link.title, link %><br>
        <small class="author">Submitted <%= time_ago_in_words(link.created_at) %> ago by <%= link.user.name %></small>
    </h2>
Check your work:


Sweet success.


Even sweeter success. Wonderful. Now we need to do the same thing to the show.html.erb page.

Update this:

<div class="page-header">
 <h1><a href="<%= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%= @link.user.email %></small></h1>
</div>
To this:

<div class="page-header">
 <h1><a href="<%= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%= @link.user.name %></small></h1>
</div>
And then we need to update the comment page, _comment.html.erb.

Update this:

<div class="pull-left">
   <p class="lead"><%= comment.body %></p>
   <p><small>Submitted <strong><%= time_ago_in_words(comment.created_at) %> ago</strong> by <%= comment.user.email %></small></p>
  </div>
To this:

<div class="pull-left">
   <p class="lead"><%= comment.body %></p>
   <p><small>Submitted <strong><%= time_ago_in_words(comment.created_at) %> ago</strong> by <%= comment.user.name %></small></p>
  </div>
And now we need to make sure that when a new user signs up, the form asks for the name. Just copy / paste this code into the new.html.erb file and save:

<div class="form-group">
        <%= f.label :name %>
        <%= f.text_field :name, class: "form-control", :autofocus => true %>
      </div>
Finally, commit and push your changes! We’re almost done.

git status
git add .
git commit -m "added name field"
git push
Wowza. That was something else, wasn’t it. We’re finished.

Hope you enjoyed the fun. Let me know if you have any questions!

Repository for this project is located here.
