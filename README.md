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
Processing by Rails::Welcome#index as HTML
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
# config/routes.rb

Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

  get '/', to: 'welcome#index'
end
```

This line that we added is telling our application that anytime we receive a `GET` request for the URI `'/'`, we should perform the `index` action within our `welcome` controller.  If this sentence was complete nonsense to you, go back and review [How the Web Works](https://github.com/turingschool/intermission-assignments/blob/master/2be/details/how_the_web_works.md) and [Intro to MVC](https://github.com/turingschool/backend-curriculum-site/blob/gh-pages/module2/lessons/intro_to_mvc.md). If you don't feel like you have a full grasp on _exactly_ what is going on here, that's ok, we will be reiterating a practicing these concepts throughout the Mod.

Go back to your browser and refresh the page - you should now be seeing an error telling you that you have a `Routing Error`; specifically, that you have an `uninitialized constant Welcome Controller`.  This is a good thing; at this point, we have told our application which controller action to perferm when this request is received, but we haven't created that controller (or the action) yet.  So, let's go do that.

Open your `controllers` directory and create a file inside called `welcome_controller.rb`.  In that file, add the following code (remember that it is *strongly* recommended that you not copy/paste, but practice typing out this code).

```ruby
# app/controllers/welcome_controller.rb

class WelcomeController < ApplicationController

  
end
```

Go back to your browser and refresh the page again.  You should now be seeing a new error `The action 'index' could not be found for WelcomeController`.  Rails is telling us that it was able to get to the controller that we wanted, but wasn't able to find the action that we had specified for this request.  Update your `welcome_controller.rb` to include this action:

```ruby
# app/controllers/welcome_controller.rb

class WelcomeController < ApplicationController
  def index

  end
end
```

Refresh the page again. New error! You should now see the following text (with some additional info): `WelcomeController#index is missing a template for this request`.  Rails is telling us that now it was able to find the controller and action we wanted, but once there, it didn't know what information to send back to the browser it the response body.  It was expecting to find some HMTL that it could send back for the browser to render, but it found nothing. Let's go create that HTML in our views directory.

In your views directory, add a sub-directory called `welcome` and, within that directory, a file called `index.html.erb`.  When creating views, we name the files the same as our action: `def index` to `index.html.erb`. And, those files will live in a directory with the same name as our controller: `WelcomeController` to `views/welcome/`.

In that file, add the following HTML:

```erb
<h1>Welcome to the Task Manager</h1>

<ul>
  <li><a href="/tasks">Task Index</a></li>
  <li><a href="/tasks/new">New Task</a></li>
</ul>
```

We have an h1 tag for our welcome message, then an unordered list (ul) with two list items (li) inside. If you are unfamiliar with HTML tags, try one of the HTML tutorials before continuing.

Inside of each li tag, we have an a tag. The href of the tag is the path where the link will go. In the first a tag, the path will be [http://localhost:3000/tasks](http://localhost:3000/tasks). The second path will be [http://localhost:3000/tasks/new](http://localhost:3000/tasks/new).

Refresh your browser and you should now see an error-free view! What do you think will happen if you click on one of these links? More errors - but that's ok! We can use these errors to help guide us in our development.

## Adding a Task Index

From the home page of your app (localhost:3000/), click on the link for `Task Index`.  You should see an error telling you that `No route matches [GET] "/tasks"`.  Our app received a request that it was not set up to handle, so let's change that!

First we need to update our `config/routes.rb` to handle a `GET '/tasks'` request.  Update your `routes.rb` file to include the following:

```ruby
# config/routes.rb
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

  get '/', to: 'welcome#index'
  get '/tasks', to: 'tasks#index'
end
```

Thinking back to the welcome page that we created, what are our next steps to get this route to work properly? In order to display an error free view, we will need to add a `Tasks Controller`, an `index` action in that controller, and an `index.html.erb` file for that controller action.

First, add a new controller to your controllers directory called `tasks_controller.rb`

```ruby
# app/controllers/tasks_controller.rb

class TasksController < ApplicationController
  def index
    @tasks = ['Task 1', 'Task 2', 'Task 3']
  end
end
```

Now, have a Tasks Controller with an index action that is holding on to a list of tasks in our instance variable `@tasks`.  Rails allows us to set up instance variables in our controllers to send information down to our views.  To see this in action, let's create a new view: `app/views/tasks/index.html.erb` and put the following code inside:

```erb
<h1>All Tasks</h1>

<% @tasks.each do |task| %>
  <h3><%= task %></h3>
<% end %>
```

Refresh your browser and check that our tasks are showing up - our index view is in ok shape now.

## Adding New Tasks

We need a route that will bring a user to a form where they can enter a new task. This is the second link we had on our welcome page. In our routes.rb file:

```ruby
# config/routes.rb

Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

  get '/', to: 'welcome#index'
  get '/tasks', to: 'tasks#index'
  get '/tasks/new', to: 'tasks#new'
end
```

And then in our tasks controller: 

```ruby
# app/controllers/tasks_controller.rb

class TasksController < ApplicationController
  def index
    @tasks = ['Task 1', 'Task 2', 'Task 3']
  end

  def new
  end
end
```

In this case, we don't need an instance variable, we just need to render the new view, so we can leave the method as is!

And now we will create a new view for `tasks/new.html.erb` and include the following:

```erb
<form action="/tasks" method="post">
  <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
  <p>Enter a new task:</p>
  <input type='text' name='task[title]'/><br/>
  <textarea name='task[description]'></textarea><br/>
  <input type='submit'/>
</form>
```

Here we have a form with an action (url path) of /tasks and a method of post. This combination of path and verb will be important when we create the route in the controller. We then have an text input field for the title, a textarea for the description, and a submit button.

Navigate to [http://localhost:3000/tasks/new](http://localhost:3000/tasks/new) to see your beautiful form!

Try clicking the submit button. You should get a `No route matches [POST] "/tasks"` error because we haven't set up a route in our app to handle this method/action (POST '/tasks') combination from the form submission.

In our `routes.rb`:

```ruby
# config/routes.rb

Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

  get '/', to: 'welcome#index'
  
  get '/tasks', to: 'tasks#index'
  get '/tasks/new', to: 'tasks#new'
  post '/tasks', to: 'tasks#create'
end
```

And in our `tasks_controller.rb`:

```ruby
# app/controllers/tasks_controller.rb

class TasksController < ApplicationController
  def index
    @tasks = ['Task 1', 'Task 2', 'Task 3']
  end

  def new
  end

  def create
  end
end
```
