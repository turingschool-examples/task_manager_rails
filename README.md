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

## Getting Started

Before creating our new Task Manager app, let's make sure we are all on the same version of Rails.  For this tutorial, you will want to be running Rails 5.1.x.  To check which version of rails you have installed, run `$ rails -v`.  If you see any version other than 5.1.x, you will need to follow [these instructions]() to get the correct version installed.

After confirming that you are running the correct version of rails, we are ready to get started!

To create your rails app, navigate to your 2module directory and run the following command:

`$ rails new task_manager -T -d="postgresql"`

Let's break down this command to better understand what is happening.  `rails new` is the command to create a new Rails app - this will create *a lot* of directories and files; we will explore this file structure in a moment.  `task_manager` is going to be the name of the directory in which our Rails app lives and is, essentially, the name of our application. `-T` tells Rails that we are not going to use its default testing suite.  During the module, we will be using a new testing framework, RSpec, and will save that for class time. `-d="postgresql"` tells Rails that we will be using a postgresql database; Rails can handle many different databases, but this is the one we will be using in Mod2.

Now that we have created our rails app, let's `cd task_manager` and explore some of the structure that Rails builds out for us.

<p align="center">
  ![Imgur](https://i.imgur.com/dHXvRD4.png?1)
</p>

Taking a look at the files that rails creates can be a bit overwhelming at first, but don't worry - this tutorial will only touch on a handful of directories!  The top level directories that we are concered with are:
* app - This is where our MVC structure lives.
* config - Inside this directory, in the routes.rb file is where we will tell our Rails app which HTTP requests to respond to.
* db - Where our database structure will be set up.

In addition to these directories, we will also be dealing with our Gemfile, which is where we will tell Rails about any other gems 
