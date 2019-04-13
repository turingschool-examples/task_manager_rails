# Task Manager

## CRUD in Rails

What does CRUD (in the the programming world) stand for?

* C: Create
* R: Read
* U: Update
* D: Delete

The apps that you will be creating in Mod 2 will make heavy use of these four actions. 

Let's use Rails to build an application where we can manage some tasks.

We're going to follow the [MVC](http://backend.turing.io/module2/lessons/intro_to_mvc) design pattern, which Rails uses by default, to implement the CRUD actions for our Task Manager app.

Throughout the module, we'll talk through some conventions and best practices, but for now - we'd like for you to follow along with this tutorial. We highly recommend **not** copying and pasting the code in this tutorial. It's to your advantage to type each line of code on your own. 

## Getting Configured

Before creating our new Task Manager app, let's make sure we are all on the same version of Rails.  For this tutorial, you will want to be running Rails 5.1.x.  To check which version of rails you have installed, run `$ rails -v`.  If you see any version other than 5.1.x, you will need to follow [these instructions]() to get the correct version installed.

After confirming that you are running the correct version of rails, we are ready to get started!

To create your rails app, navigate to your 2module directory and run the following command:

`$ rails new task_manager -T -d="postgresql"`

Let's break down this command to better understand what is happening.  `rails new` is the command to create a new Rails app - this will create *a lot* of directories and files; we will explore this file structure in a moment.  `task_manager` is going to be the name of the directory in which our Rails app lives and is, essentially, the name of our application. `-T` tells Rails that we are not going to use its default testing suite.  During the module, we will be using a new testing framework, RSpec, and will save that for class time. `-d="postgresql"` tells Rails that we will be using a postgresql database; Rails can handle many different databases, but this is the one we will be using in Mod2.

Now that we have created our rails app, let's `cd task_manager` and explore some of the structure that Rails builds out for us.

## Project Folder Structure

<p align="center">
  <img src="https://i.imgur.com/dHXvRD4.png?1" title="source: imgur.com" />
</p>

Taking a look at the files that rails creates can be a bit overwhelming at first, but don't worry - this tutorial will only touch on a handful of directories!  The top level directories that we are concered with are:
* *app* - This is where our MVC structure lives.
* *config* - Inside this directory, in the routes.rb file is where we will tell our Rails app which HTTP requests to respond to.
* *db* - Where our database structure will be set up.

In addition to these directories, we will also be dealing with our Gemfile, which is where we will tell Rails about any other gems we might need to run our app. For our task manager, we will leave the Gemfile as is.

## Getting the App Running

Before we can see what our new rails app can do, we will need to get our server up and running and ready for HTTP requests. To do this, run either

`rails server` or `rails s`

You should see something like this:

```
=> Booting Puma
=> Rails 5.1.7 application starting in development 
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.12.1 (ruby 2.4.1-p111), codename: Llamas in Pajamas
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://localhost:3000
Use Ctrl-C to stop
```

Navigate to [http://localhost:3000/](http://localhost:3000/) and you should see some Rails magic! 

Now, let's take a look back at your terminal and walkthrough what just happened.

```
Started GET "/" for ::1 at 2019-04-13 14:38:02 -0600
Processing by Rails::WelcomeController#index as HTML
  Rendering /Users/meganmcmahon/.rbenv/versions/2.4.1/lib/ruby/gems/2.4.0/gems/railties-5.1.7/lib/rails/templates/rails/welcome/index.html.erb
  Rendered /Users/meganmcmahon/.rbenv/versions/2.4.1/lib/ruby/gems/2.4.0/gems/railties-5.1.7/lib/rails/templates/rails/welcome/index.html.erb (2.6ms)
Completed 200 OK in 254ms (Views: 9.1ms)
```

On the first line, you are seeing a snapshot of the HTTP request that was received by our server when we navigated to localhost:3000 - `GET "/"`.  This is basically telling us that our application received a request for the information that lives at a certain address.

On the last line, we can see a snapshot of the response that our server sent back to the browser `Completed 200 OK`. Meaning, we received a request, were able to process that request and succesfully send back a response.  The response is what contains the information that allows the browser to render the page for `Yay! You're on Rails!`

## Using Views

Let's update our app to show something other than the default Rails welcome - this is, after all, *your app*!

The first thing we need to do, is tell our app how to handle specific requests, we will do this by adding the following code to our `config/routes.rb` file:

```ruby
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

  get '/', to: 'welcome#index'
end
```
