# Task Manager 7

## CRUD in Rails

What does CRUD (in the the programming world) stand for?

- C: Create
- R: Read
- U: Update
- D: Delete

The apps that you will be creating in Mod 2 will make heavy use of these four actions.

Let's use Rails to build an application where we can manage some tasks.

We're going to follow the MVC design pattern, which Rails uses by default, to implement the CRUD actions for our Task Manager app. For a quick explanation of MVC, please read through [this](https://medium.freecodecamp.org/simplified-explanation-to-mvc-5d307796df30) post.

Throughout the module, we'll talk through some conventions and best practices, but for now - we'd like for you to follow along with this tutorial. We highly recommend **not** copying and pasting the code in this tutorial. It's to your advantage to type each line of code on your own.

## Getting Configured

Before creating our new Task Manager app, let's make sure we are all on the same version of Rails. For this tutorial, you will want to be running Rails 7.1.2. To check which version of rails you have installed, run `$ rails -v`. If you see any version other than 7.1.2, you will need to follow [these instructions](./rails_uninstall.md) to get the correct version installed.

After confirming that you are running the correct version of rails, we are ready to get started!

To create your rails app, navigate to your 2module directory and run the following command:

`$ rails new task_manager -T -d="postgresql"`

Let's break down this command to better understand what is happening. `rails new` is the command to create a new Rails app - this will create *a lot* of directories and files; we will explore this file structure in a moment. `task_manager` is going to be the name of the directory in which our Rails app lives and is, essentially, the name of our application. `-T` tells Rails that we are not going to use its default testing suite. We will be using RSpec as our testing framework. `-d="postgresql"` tells Rails that we will be using a postgresql database; Rails can handle many different databases, but this is the one we will be using in Mod 2.

Now that we have created our rails app, let's `cd task_manager` and explore some of the structure that Rails builds out for us.

## Project Folder Structure

![Untitled](./images/rails_7_file_structure.png)

Taking a look at the files that rails creates can be a bit overwhelming at first, but don't worry - this tutorial will only touch on a handful of directories! The top level directories that we are concered with are:

- *app* - This is where our MVC structure lives.
- *config* - Inside this directory, in the routes.rb file is where we will tell our Rails app which HTTP requests to respond to.
- *db* - Where our database structure will be set up.

In addition to these directories, we will also be dealing with our Gemfile, which is where we will tell Rails about any other gems we might need to run our app. For our task manager we will be adding just one gem to our Gemfile. Open your gemfile and add `pry` to the `:development, :test` group - your Gemfile should now look like this:

```ruby
source "https://rubygems.org"
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby "3.2.2"

# Bundle edge Rails instead: gem "rails", github: "rails/rails", branch: "main"
gem "rails", "~> 7.1.2"

# The original asset pipeline for Rails [https://github.com/rails/sprockets-rails]
gem "sprockets-rails"

# Use postgresql as the database for Active Record
gem "pg", "~> 1.1"

# Use the Puma web server [https://github.com/puma/puma]
gem "puma", "~> 6.0"

# Use JavaScript with ESM import maps [https://github.com/rails/importmap-rails]
gem "importmap-rails"

# Hotwire's SPA-like page accelerator [https://turbo.hotwired.dev]
gem "turbo-rails"

# Hotwire's modest JavaScript framework [https://stimulus.hotwired.dev]
gem "stimulus-rails"

# Build JSON APIs with ease [https://github.com/rails/jbuilder]
gem "jbuilder"

# Use Redis adapter to run Action Cable in production
# gem "redis", "~> 4.0"

# Use Kredis to get higher-level data types in Redis [https://github.com/rails/kredis]
# gem "kredis"

# Use Active Model has_secure_password [https://guides.rubyonrails.org/active_model_basics.html#securepassword]
# gem "bcrypt", "~> 3.1.7"

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem "tzinfo-data", platforms: %i[ mingw mswin x64_mingw jruby ]

# Reduces boot times through caching; required in config/boot.rb
gem "bootsnap", require: false

# Use Sass to process CSS
# gem "sassc-rails"

# Use Active Storage variants [https://guides.rubyonrails.org/active_storage_overview.html#transforming-images]
# gem "image_processing", "~> 1.2"

group :development, :test do
  # See https://guides.rubyonrails.org/debugging_rails_applications.html#debugging-with-the-debug-gem
  gem "debug", platforms: %i[ mri mingw x64_mingw ]
  gem "pry"
end

group :development do
  # Use console on exceptions pages [https://github.com/rails/web-console]
  gem "web-console"

  # Add speed badges [https://github.com/MiniProfiler/rack-mini-profiler]
  # gem "rack-mini-profiler"

  # Speed up commands on slow machines / big apps [https://github.com/rails/spring]
  # gem "spring"
end
```

Any time we update our Gemfile, we will need to tell our application to install or update the additional gems. In your terminal, run the command:

```bash
$ bundle install
```

Great - now we can use `binding.pry` anywhere in our app to debug as we go!

## Database Set-Up

Before we can see what our new rails app can do, we need to do some more set up. First, let's create our app's database. Do this from the command line with:

```bash
$ rails db:create
```

You should see some output like this:

```bash
Created database 'task_manager_development'
Created database 'task_manager_test'
```

We have created two databases, a "development" and a "test" database. In this tutorial, we won't be writing any tests so we won't touch our test database. We are working in the "development" environment.

Great, now our database is created! But, we don't have any tables yet.

### Migrating to our Database

We could potentially create a tasks table directly from our terminal, but that's no fun, and it makes it pretty difficult to work with other people. Instead, let's create some migrations.

Migrations allow you to evolve your database structure over time. Each migration includes instructions to make some change to the database (e.g. adding a table, adding columns to a table, dropping a table, etc.). One advantage to this approach is that it will allow you to transfer the application to different computers without transferring the whole database. This isn't a big problem for us now, but as your database grows it will be advantageous to be able to transfer the instructions to create the database instead of the database itself.

To create a migration that will send instructions to create a tasks table to our database, run the following command from your terminal:

```bash
$ rails generate migration CreateTask title:string description:string
```

In this command, we are telling rails to generate a migration file that will create a tasks table in our database with two columns - title and description. To see the migration that rails created, open your `db/migrate` directory, and you should have a file in there that is called something like `db/migrate/20190414173402_create_task.rb`. Open that file and you will see the following:

```ruby
class CreateTask < ActiveRecord::Migration[7.0]
  def change
    create_table :tasks do |t|
      t.string :title
      t.string :description

      t.timestamps
    end
  end
end
```

So, now we have a migration with some instructions to tell our database to create a tasks table, but how do we actually get the table created? Run the following in your terminal:

```bash
$ rails db:migrate
```

And you should see something like this:

```bash
== 20221130062449 CreateTask: migrating =======================================
-- create_table(:tasks)
   -> 0.0052s
== 20221130062449 CreateTask: migrated (0.0053s) ==============================
```

Great! How can we verify that worked?

In your terminal, connect to the database that we just created, and see if we can select some information from our tasks table:

```bash
$ rails dbconsole
```

```bash
psql (14.2)
Type "help" for help.

task_manager_development=# SELECT * FROM tasks;
 id | title | description | created_at | updated_at
----+-------+-------------+------------+------------
(0 rows)

task_manager_development=#
```

Awesome - we have a database with a table for tasks! No records yet, but that's ok - all we needed to know was that our database is up a configured correctly.

To exit the psql session, enter the command `exit`

## Getting the App Running

Now that we've set up our database, it's time to get our server up and running and ready for HTTP requests. To do this, run either

`rails server` or `rails s`

You should see something like this:

```bash
=> Booting Puma
=> Rails 7.1.2 application starting in development
=> Run `bin/rails server --help` for more startup options
Puma starting in single mode...
* Puma version: 5.6.5 (ruby 3.2.2-p18) ("Birdie's Version")
*  Min threads: 5
*  Max threads: 5
*  Environment: development
*          PID: 66200
* Listening on http://127.0.0.1:3000
* Listening on http://[::1]:3000
Use Ctrl-C to stop
```

Navigate to [http://localhost:3000/](http://localhost:3000/) and you should see some Rails magic!

Now, let's take a look back at your terminal and walk through what just happened.

```bash
Started GET "/" for ::1 at 2022-11-29 18:51:51 -0700
Processing by Rails::WelcomeController#index as HTML
  Rendering /Users/mdao/.rbenv/versions/3.2.2/lib/ruby/gems/3.1.0/gems/railties-7.0.5/lib/rails/templates/rails/welcome/index.html.erb
  Rendered /Users/mdao/.rbenv/versions/3.2.2/lib/ruby/gems/3.1.0/gems/railties-7.0.5/lib/rails/templates/rails/welcome/index.html.erb (Duration: 1.4ms | Allocations: 635)
Completed 200 OK in 13ms (Views: 4.9ms | ActiveRecord: 0.0ms | Allocations: 5094)
```

On the first line, you are seeing a snapshot of the HTTP request that was received by our server when we navigated to localhost:3000 - `GET "/"`. This is basically telling us that our application received a request for the information that lives at a certain address.

On the last line, we can see a snapshot of the response that our server sent back to the browser `Completed 200 OK`. Meaning, we received a request, were able to process that request and succesfully send back a response. The response is what contains the information that allows the browser to render the default page for your Rails app!

## Using Views

Let's update our app to show something other than the default Rails welcome - this is, after all, *your app*!

The first thing we need to do, is tell our app how to handle specific requests, we will do this by adding the following code to our `config/routes.rb` file:

**config/routes.rb**

```ruby
Rails.application.routes.draw do
  # Define your application routes per the DSL in https://guides.rubyonrails.org/routing.html

  # Defines the root path route ("/")
  # root "articles#index"

  get "/", to: "welcome#index"
end
```

This line that we added is telling our application that anytime we receive an HTTP `GET` request for the URI `'/'`, we should perform the `index` action within our `welcome` controller.

Go back to your browser and refresh the page - you should now be seeing an error telling you that you have a `Routing Error`; specifically, that you have an `uninitialized constant WelcomeController`. This is a good thing; at this point, we have told our application which controller action to perform when this request is received, but we haven't created that controller (or the action) yet. So, let's go do that.

Open your `app/controllers` directory and create a file inside called `welcome_controller.rb`. In that file, add the following code (remember that it is *strongly* recommended that you not copy/paste, but practice typing out this code).

**app/controllers/welcome_controller.rb**

```ruby
class WelcomeController < ApplicationController

end
```

Go back to your browser and refresh the page again. You should now be seeing a new error `The action 'index' could not be found for WelcomeController`

Rails is telling us that it was able to get to the controller that we wanted, but wasn't able to find the action that we had specified for this request. Update your `welcome_controller.rb`
 to include this action:

**app/controllers/welcome_controller.rb**

```ruby
class WelcomeController < ApplicationController
  def index

  end
end
```

Refresh the page again. New error! You should now see the following text (with some additional info): `WelcomeController#index is missing a template for request formats: text/html`. Rails is telling us that now it was able to find the controller and action we wanted, but once there, it didn't know what information to send back to the browser in the response body. It was expecting to find some HTML that it could send back for the browser to render, but it found nothing. Let's go create that HTML in our views directory.

In your `app/views` directory, add a sub-directory called `welcome` and, within that directory, a file called `index.html.erb`. When creating views, we name the files the same as our action: `def index` to `index.html.erb`. And, those files will live in a directory with the same name as our controller: `WelcomeController` to `views/welcome/`. The path for your new file should be `app/views/welcome/index.html.erb`.

In that file, add the following HTML:

**app/views/welcome/index.html.erb**

```html
<h1>Welcome to the Task Manager</h1>

<ul>
  <li><a href="/tasks">Task Index</a></li>
  <li><a href="/tasks/new">New Task</a></li>
</ul>
```

We have an h1 tag for our welcome message, then an unordered list (ul) with two list items (li) inside. If you are unfamiliar with HTML tags, try one of the HTML tutorials before continuing.

Inside of each li tag, we have an `<a>` tag. The href of the tag is the path where the link will go. In the first a tag, the path will be [http://localhost:3000/tasks](http://localhost:3000/tasks). The second path will be [http://localhost:3000/tasks/new](http://localhost:3000/tasks/new).

Refresh your browser and you should now see an error-free view! What do you think will happen if you click on one of these links? More errors - but that's ok! We can use these errors to help guide us in our development.

## Adding a Task Index

From the home page of your app (localhost:3000/), click on the link for `Task Index`. You should see an error telling you that `No route matches [GET] "/tasks"`. Our app received a request that it was not set up to handle, so let's change that!

First we need to update our `config/routes.rb` to handle a `GET '/tasks'` request. Update your `routes.rb` file to include the following:

**config/routes.rb**

```ruby
Rails.application.routes.draw do
  # Define your application routes per the DSL in https://guides.rubyonrails.org/routing.html

  # Defines the root path route ("/")
  # root "articles#index"

  get "/", to: "welcome#index"

  get "/tasks", to: "tasks#index"
end
```

Thinking back to the welcome page that we created, what are our next steps to get this route to work properly? In order to display an error free view, we will need to add a `TasksController`, an `index` action in that controller, and an `index.html.erb` file for that controller action.

First, add a new controller to your controllers directory called `tasks_controller.rb`

**app/controllers/tasks_controllers.rb**

```ruby
class TasksController < ApplicationController
  def index
    @tasks = ["Task 1", "Task 2", "Task 3"]
  end
end
```

Now, we have a Tasks Controller with an index action that is holding on to a list of tasks in our instance variable `@tasks`. Rails allows us to set up instance variables in our controllers to send information down to our views. To see this in action, let's create a new view: `app/views/tasks/index.html.erb` and put the following code inside:

**app/views/tasks/index.html.erb**

```html
<%= @tasks %>
```

Wait, what's this new `<%= %>` syntax? This is called an `erb` tag - erb stands for 'embedded ruby'. A combination of our file format `.html.erb` and the use of these tags allows us to execute ruby code directly in our html files. **erb tags have access to any instance variables created in our controller action.** So that's how we are able to access `@tasks`. Refresh your page and you will see your array displayed as… an array. Let’s add some more formatting to our view.

**app/views/tasks/index.html.erb**

```html
<h1>All Tasks</h1>

<% @tasks.each do |task| %>
  <h3><%= task %></h3>
<% end %>
```

Remember, with erb tags, we can run any Ruby code, including an `.each`! Notice how the `each`/`end` is in a slightly different erb tag, `<% %>`, that doesn't have the `=`. The difference between the two is that `<%= %>` will echo (or display) the return value of the ruby commands in your view, and the `<% %>` will not echo (or display) the return value of the ruby commands. So, in the view that we just created, we *do* want the user to see the return value of each `task` in a `h3` tag, but we *do not* want the user to see the return value of the `each` method. Try to change the tag for the each to `<%= %>` and see what happens (make sure to change it back).

Refresh your browser and check that our tasks are showing up - our index view is in ok shape now.

## Adding New Tasks

We need a route that will bring a user to a form where they can enter a new task. This is the second link we had on our welcome page. In our routes.rb file:

**config/routes.rb**

```ruby
# config/routes.rb

Rails.application.routes.draw do
  # Define your application routes per the DSL in https://guides.rubyonrails.org/routing.html

  # Defines the root path route ("/")
  # root "articles#index"

  get "/", to: "welcome#index"

  get "/tasks", to: "tasks#index"
  get "/tasks/new", to: "tasks#new"
end
```

And then in our tasks controller:

**app/controllers/tasks_controller.rb**

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

In this case, we don’t need an instance variable, we just need to render the new view, so we can leave the method as is!

And now we will create a new view for `tasks/new.html.erb` and include the following:

**app/views/tasks/new.html.erb**

```html
<form action="/tasks" method="post">
  <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
  <p>Enter a new task:</p>
  <input type='text' name='title'/><br/>
  <textarea name='description'></textarea><br/>
  <input type='submit'/>
</form>
```

Here we have a form with an action (url path) of `/tasks` and a method of `post`. This combination of path and verb will be important when we create the route, controller and action. We then have an text input field for the title, a textarea for the description, and a submit button.

_side note_: You may be wondering about this `form_authenticity_token` business - don't worry about this now; it has to do with security protocols in Rails. If you are *super* curious, take that line out and see what happens - then come back and put that line back in when your form no longer works!

Navigate to [http://localhost:3000/tasks/new](http://localhost:3000/tasks/new) to see your beautiful form!

Try clicking the submit button. The form should reload, but if you look at your terminal, you should see a `ActionController::RoutingError (No route matches [POST] "/tasks"):` error because we haven't set up a route in our app to handle this method/action (POST "/tasks") combination from the form submission, and so Rails will just reload our form.

In our `routes.rb`

**config/routes.rb**

```ruby
# config/routes.rb

Rails.application.routes.draw do
  # Define your application routes per the DSL in https://guides.rubyonrails.org/routing.html

  # Defines the root path route ("/")
  # root "articles#index"

  get "/", to: "welcome#index"

  get "/tasks", to: "tasks#index"
  get "/tasks/new", to: "tasks#new"
  post "/tasks", to: "tasks#create"
end
```

And in our `tasks_controller.rb`

**app/controllers/tasks_controller.rb**

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

Navigate to [http://localhost:3000/tasks/new](http://localhost:3000/tasks/new) and fill in the form that you see there, and click on 'Submit'. What happened? Is this what you expected? Based on our form setup, when we click 'Submit', we are sending a new request to our app `POST '/tasks'`. This request will first hit our routes.rb file, where Rails will use the route that we set up as `POST '/tasks', to: "tasks#create"` to perform the `create` action in our `tasks controller`. Up to now, when we have asked Rails to perform any actions for which there is no view set up, we have seen an error indicating that there is no view for a specific action. Why did we not see this error when submitting our form? Rails is set up to try to anticipate our needs as web developers and will often provide some default functionality, that we commonly refer to as Rails Magic; and that is what is happening here! Let's dig in a bit more to what Rails is doing when we click on this submit button.

Check out your terminal and you should see something like this:

```bash
Started POST "/tasks" for 127.0.0.1 at 2022-11-29 23:05:39 -0700
Processing by TasksController#create as TURBO_STREAM
  Parameters: {"authenticity_token"=>"[FILTERED]", "title"=>"New Task", "description"=>"This is a new task!"}
No template found for TasksController#create, rendering head :no_content
Completed 204 No Content in 1ms (Allocations: 380)
```

On the first line, we see that our app received a request to `POST` to `'/tasks'`. As our app started trying to handle this request, we eventually got to a point where there was no HTML to render, so Rails attempted to smooth over our user's experience by sending back a response indicating that there is no content in the response. We see this on the second to last line above `No template found for TasksController#create, rendering head :no_content`. And, instead of throwing an error, Rails has resolved this response with a successful status code: `Completed 204 No Content`.

The reason that Rails has provided this *magic* for us, instead of throwing an error, is because Rails is assuming that when any new record is created, we will want to redirect our user's to some new place where they can see this change in place. Rails will assume that for any HTTP POST, PATCH, PUT, and DELETE request, we will want to send a redirect in our response. So, for these requests and their controller actions, Rails will not error if there is no corresponding HTML in our views folder!

Now let's get back to actually creating this new task. Looking back at our terminal output from submitting the form, there is a bunch of stuff right in the middle that we have not yet discussed. Specifically, let's take a look at the `parameters:` section of the output:

```bash
Parameters: {"authenticity_token"=>"[FILTERED]", "title"=>"New Task", "description"=>"This is a new task!"}
```

Looking through this information, there are a few things we will want to focus on. First, the basic structure of the parameters should look familiar - it looks like a Hash! We will be able to work with these parameters in much the same way we worked with Hashes in Mod1. Additionally, this parameters 'hash' includes the information that we sent in through our form as a set of key/value pairs that match up with our form fields and the information that we (as a user) sent in when we submitted the form.

Let's take a look at how we can access this information from inside our application. Throw a pry in your create action so that your controller looks like this:

**app/controllers/tasks_controller.rb**

```ruby
class TasksController < ApplicationController
  def index
    @tasks = ['Task 1', 'Task 2', 'Task 3']
  end

  def new
  end

  def create
    binding.pry
  end
end
```

Now, go back to your browser and click on 'Submit' again, then open your terminal, and you should be in a pry session that looks like this:

```bash
Started POST "/tasks" for 127.0.0.1 at 2022-11-29 23:12:54 -0700
Processing by TasksController#create as TURBO_STREAM
  Parameters: {"authenticity_token"=>"[FILTERED]", "title"=>"New Task", "description"=>"This is a new task!"}

From: /Users/mdao/turing/task_manager/app/controllers/tasks_controller.rb:10 TasksController#create:

     9: def create
 => 10:   binding.pry
    11: end

[1] pry(#<TasksController>)>
```

Type in `params` to see some more Rails magic:

```bash
[1] pry(#<TasksController>)> params
=> #<ActionController::Parameters {"authenticity_token"=>"NPcAMoAPxAaXRH0cJ--64cw9q1mBaymSfU6sotrwhP0OH0-fSN6OkJboLFIy9eG_lMK5r4T6YR3gHYpZ9rehxQ", "title"=>"New Task", "description"=>"This is a new task!", "controller"=>"tasks", "action"=>"create"} permitted: false>
[2] pry(#<TasksController>)>
```

Rails has taken the parameters that we received in this request and turned it into a `params` object that we can use and manipulate inside of our application! Play around with this params object to get the two pieces of information we need for our new task - 'title' and 'description'.

```bash
[1] pry(#<TasksController>)> params["title"]
=> "New Task"
[2] pry(#<TasksController>)> params["description"]
=> "This is a new task!"
[3] pry(#<TasksController>)> params[:title]
=> "New Task"
[4] pry(#<TasksController>)> params[:description]
=> "This is a new task!"
```

Taking a look at the commands and their results above, can you spot a difference between the Hashes that you worked with in Mod1 and the params object that rails builds for us?

When working with a Ruby Hash, we use the key *exactly* as it is created in order to access the value at that location - if a key is set up as a String, we must use a String to access the value. But, with our Rails params object, we can use either a String or a Symbol to access values of a specific key. At this point, it is important to understand this only because it is conventional to use a symbol to access these values, and that is what we will be doing for this tutorial, and in Mod2.

Go back to your terminal and `exit` your pry session to complete the response we interrupted:

```bash
[8] pry(#<TasksController>)> exit
No template found for TasksController#create, rendering head :no_content
Completed 204 No Content in 208904ms (ActiveRecord: 0.0ms | Allocations: 31596)
```

## Saving Tasks

So we're able to get information in from our form, but we haven't really done much with it yet. It would be great if we could save it somewhere...\*\*

### Our Approach

Let's think for a little bit about what we'd like to do with these task params that we're getting when our form is submitted. Really what we want to do here is create a new task using those parameters, and then save it somewhere that we can retrieve it (like a database!).

For now, let's ignore the particulars of that problem. We're going to program with enough faith in ourselves that we can figure those things out when we get to them.

I'm sure we could figure out a way to do everything we needed to do in this file, but right now, as a programmer with some OO background, sitting in my controller, thinking about creating and saving new tasks makes me wish that I had some sort of Task class that I could use to create Task objects.

### Creating A Task Class (Model)

Let's change the code inside of our controller to use this Task class that we're thinking about creating. If it were up to me (and it is), I would create a new task with my task params and then save it so that I could find it later. Let's assume that we're going to create a class that does just that. Let's update our create action in our tasks controller:

**app/controllers/tasks_controller.rb**

```ruby
def create
  task = Task.new({
    title: params[:title],
    description: params[:description]
    })

  task.save

  redirect_to '/tasks'
end
```

You'll sometimes see this I'm-going-to-assume-I-have-a-thing-that-works approach referred to as "top-down" programming. For an awesome video demonstration take a look at [this](https://vimeo.com/131588133) when you have a moment. The opposite approach would be "bottom-up" where we start with the smallest piece that we can get to work and start building on it. Both are totally valid.

In order to follow MVC conventions, we are going to create this Task class in a `app/models/task.rb` file. In that file, create a Task class that looks like this:

**app/models/task.rb**

```ruby
class Task < ApplicationRecord

end
```

Why inherit from `ApplicationRecord`? This Task class that we are creating is meant to be a very specific and specialized class that we refer to as a Model. The purpose of Models is to create objects based on records that exist in a database. Rails give us some methods that we can use to help in this creation and we inherit those methods from `ApplicationRecord`. Two of the methods that we inherit are `::new` and `#save`, which is great because these are two methods that we are calling in our tasks controller!

When we use the `new` method, Rails is going to attempt to create an object with attributes that match up to column names that exist in a `tasks` table in our database; and, when we attempt to `save` that object, Rails will try to `INSERT` a new record in our database with those attributes. Can you see where we might run into problems? We haven't set up our database yet, much less any tables in that database!

### Reviewing Our Database Set-up

At the start of this tutorial we set up our database. Let's review what we did.

First, we created our database with this command:

```bash
$ rails db:create
```

And got this output:

```bash
Created database 'task_manager_development'
Created database 'task_manager_test'
```

We created two databases, a "development" and a "test" database. For this tutorial we are working in the "development" environment because we are not writing tests.

We then created a migration with the instructions for creating a tasks table in our database by running the following command:

```bash
$ rails generate migration CreateTask title:string description:string
```

In this command, we told rails to generate a migration file that will create a tasks table in our database with two columns - title and description. You can take a look at your migration in a file named something like `db/migrate/20190414173402_create_task.rb`.

We then ran the following code to apply our migration and actually create a database.

```bash
$ rails db:migrate
```

Let's review what our table currently looks like. In your terminal, connect to the database, and see what's currently in your task table.

```bash
$ rails dbconsole
```

```bash
psql (14.2)
Type "help" for help.

task_manager_development=# SELECT * FROM tasks;
 id | title | description | created_at | updated_at
----+-------+-------------+------------+------------
(0 rows)

task_manager_development=#
```

You may have some tasks in your table, or not. Either way is fine!

To exit the psql session, enter the command `exit`

### Writing to our Database

Now that we have our Task model created and our database ready to hold on to new tasks, lets start up our server again by running `rails s`
 and let's put three `binding.pry`'s in our tasks controller's create action so that it looks like this:

**app/controllers/tasks_controller.rb**

```ruby
  def create
    binding.pry
    task = Task.new({
      title: params[:title],
      description: params[:description]
      })
    binding.pry
    task.save
    binding.pry
    redirect_to '/tasks'
  end
```

Navigate to [http://localhost:3000/tasks/new](http://localhost:3000/tasks/new), fill out our form, and click 'Submit'. In our terminal, we should have hit our first pry - let's make sure we have access to the information we need to create a new task:

```bash
9: def create
=> 10:   binding.pry
  11:   task = Task.new({
  12:     title: params[:title],
  13:     description: params[:description]
  14:     })
  15:   binding.pry
  16:   task.save
  17:   binding.pry
  18:   redirect_to '/tasks'
  19: end

[1] pry(#<TasksController>)> params
=> #<ActionController::Parameters {"authenticity_token"=>"NPcAMoAPxAaXRH0cJ--64cw9q1mBaymSfU6sotrwhP0OH0-fSN6OkJboLFIy9eG_lMK5r4T6YR3gHYpZ9rehxQ",{"title"=>"New Task", "description"=>"This is a new task!", "controller"=>"tasks", "action"=>"create"} permitted: false>
```

Looks good, let's `exit` to hit our next pry and see what `task` looks like at this point:

```bash
	9: def create
  10:   binding.pry
  11:   task = Task.new({
  12:     title: params[:title],
  13:     description: params[:description]
  14:     })
=> 15:   binding.pry
  16:   task.save
  17:   binding.pry
  18:   redirect_to '/tasks'
  19: end

[1] pry(#<TasksController>)> task
=> #<Task:0x0000000113de81b8 id: nil, title: "New Task", description: "This is a new task!", created_at: nil, updated_at: nil>
```

We have a Task object! We can see that it has a title and a description, but no id; this is telling us that Rails was able to create the object, but has not yet saved it in the database. Let's `exit` to hit our next pry and see if anything changes.

```bash
9: def create
  10:   binding.pry
  11:   task = Task.new({
  12:     title: params[:title],
  13:     description: params[:description]
  14:     })
  15:   binding.pry
  16:   task.save
=> 17:   binding.pry
  18:   redirect_to '/tasks'
  19: end

[1] pry(#<TasksController>)> task
=> #<Task:0x0000000113de81b8
 id: 1,
 title: "New Task",
 description: "This is a new task!",
 created_at: Wed, 30 Nov 2022 06:36:00.036513000 UTC +00:00,
 updated_at: Wed, 30 Nov 2022 06:36:00.036513000 UTC +00:00>
```

Now, our task has an id, indicating that it was saved in our database! Great, it looks like everything is working, so let's `exit` through this last pry and go check on our browser.

We are now looking at our task index again, but we don't see our new task - why is that? If we look at our index action, we are setting up an array of Strings, rather than asking our database for all the tasks that currently exist. Let's change that!

### Reading from our Database

In the same way that we inherited methods to create database records, we have also inherited methods to get `all` records that currently exist in the database. Let's update our index action in our tasks controller to match the following:

**app/controllers/tasks_controller.rb**

```ruby
  def index
    @tasks = Task.all
  end
```

Now, refresh your browser - what do you see? You are now seeing... something... but not really what we expected, right? Before, we were sending an array of strings to our view and that view was iterating over the array and creating a list of all the things in that array. It is still doing that, but now, the things that are in the array are objects, not just strings, so the way we treat them will be slightly different. Go to your index view and change it to work with task objects instead.

**app/views/tasks/index.html.erb**

```html
<h1>All Tasks</h1>

<% @tasks.each do |task| %>
  <h3><%= task.title %></h3>
  <p><%= task.description %></p>
<% end %>
```

Refresh your browser and you should see your new task!\*\*

### Showing Individual Tasks

Let's see if we can show some individual tasks. At this point we should be able to rely on a lot of the pieces we have already set up. At a high level we want to do the following:

- Add a link to an individual task from the index
- Add a route for an individual task
- In our controller, find a specific task to send to a view
- Create a view to show details for a specific task

### Add a Link to an Individual Task

From a users perspective, it makes the most sense to have a link to each task directly from the tasks index page. Let's update our `app/views/tasks/index.html.erb` to include this link:

**app/views/tasks/index.html.erb**

```html
<h1>All Tasks</h1>

<% @tasks.each do |task| %>
  <h3><a href="/tasks/<%= task.id %>"><%= task.title %></a></h3>
  <p><%= task.description %></p>
<% end %>
```

Notice how we can use erb to embed ruby into html attributes. This is a very powerful feature that we will use a lot.

Now, when we visit [http://localhost:3000/tasks](http://localhost:3000/tasks), instead of seeing just a plain text of each tasks title, we will see that title as a link to an individual task's show page.

But that link won't work just yet!

### Add a Route and Controller Action for an Individual Task

In our `config/routes.rb` file, add the following route:

**config/routes.rb**

```ruby
Rails.application.routes.draw do
  # Define your application routes per the DSL in https://guides.rubyonrails.org/routing.html

  # Defines the root path route ("/")
  # root "articles#index"
  get "/", to: "welcome#index"

  get "/tasks", to: "tasks#index"
  get "/tasks/new", to: "tasks#new"
  post "/tasks", to: "tasks#create"
  get "/tasks/:id", to: "tasks#show"
end
```

And in our `tasks_controller.rb` add a show action:

**app/controllers/tasks_controller.rb**

```ruby
def show

end
```

### Add a Show View

Before we get to what will live inside this show action, let's dream up what we might want to be in our view for this action.

In our views folder, create a file for `app/views/tasks/show.html.erb` and put the following HTML inside it:

**app/views/tasks/show.html.erb**

```html
<a href="/tasks">Task Index</a>

<h1><%= @task.title %></h1>

<p><%= @task.description %></p>
```

Now, we have a show page that will show the information for a specific task, as well as a link back to the task index page.

Let's see if we have accomplished our goal - navigate to [http://localhost:3000/tasks/1](http://localhost:3000/tasks/1) and let's see what happens.

More errors! We should be seeing an error message telling us that we have a `NoMethodError in Tasks#show`, and if we look more closely, we are specifically seeing an `undefined method 'title' for nil:NilClass`. Thinking back to the errors we have seen in Mod1 - this is telling us that we are calling a method, `title` on a `nil`; in other words, `@task` is not holding on to a specific task that we can call `title` and `description` on.

### Finding Specific Tasks

Let's look back at our show action in our tasks controller and see if we can set up our instance variable `@task`. Remember when we talked about how we are inheriting some functionality from ActiveRecord that helps us interact with our database? Well, now's a great place to take advantage of the ActiveRecord method `find`, which will retrieve a record from our database based on that record's id. In this case, we can use find like this:

**app/controllers/tasks_controller.rb**

```ruby
def show
  @task = Task.find(params[:id])
end
```

Navigate to [http://localhost:3000/tasks/1](http://localhost:3000/tasks/1) and you should now see the show page for that particular task!

But wait - the last time we used params, we were getting information from a form. There's no form involved in this interaction, so what exactly is happening here? Let's throw a `binding.pry` at the top of our show action and see what we can find:

**app/controllers/tasks_controller.rb**

```ruby
def show
  binding.pry
  @task = Task.find(params[:id])
end
```

Refresh your browser and take a look at your terminal. In your pry session, call `params` and see what is returned.

```bash
9: def show
=> 10:   binding.pry
  11:   @task = Task.find(params[:id])
  12: end

[1] pry(#<TasksController>)> params
=> #<ActionController::Parameters {"controller"=>"tasks", "action"=>"show", "id"=>"1"} permitted: false>
```

We are getting a much simpler params object than when we used a form, and these params include an `:id` that matches with the very end of the uri we visited (/tasks/1). Looking at our routes, we see that we set up our URI pattern to accept `:id`, but when we visited this site, we typed in `1` which is an actual id that exists in our database. When we need to get some information, like an id, from our route in the form of parameters, we can include a symbol of the thing we are expecting when we set up our route - in this case, we are expecting an `:id`. So, based on how we set up our routes, we can manipulate and dictate what parameters we want; and what information we will need access to in our controllers.

Remember that you will need to `exit` your pry session to continue interacting with your site!

## Editing a Task

At this point, we have an application that will allow us to visit a home page, see a list of tasks, add a task, and see a specific task. We have covered the Create and Read portions of CRUD. Now, let's add some functionality to be able to Update existing tasks.

This updating will be very similar to creating new tasks. At a high level, we will need to:

- Create a button to edit a specific task
- Create a route that will GET us an edit form
  - Add the action and views for this route
- Create a route that will update a specific task based on form input
  - Add the action that will accomplish this update and redirect the user

### Add an Edit Button to the Show Page

To make our user experience as smooth as possible, let's create a link to 'Edit' a specific task - similar to the link we created to add a new task.

In our `show.html.erb` file, update the code to include an edit link:

```html
<a href="/tasks">Task Index</a>

<h1><%= @task.title %></h1>

<p><%= @task.description %></p>

<a href="/tasks/<%= @task.id %>/edit">Edit</a>
```

Now, when a user navigates to [http://localhost:3000/tasks/1](http://localhost:3000/tasks/1), they will see a link to 'Edit'. If they click on that link, a `GET` request will be sent to the URI `tasks/:id/edit`. As of now, that route doesn't exist - let's create it!

### Creating the Route, Controller Action, and View

In our `config/routes.rb` file, add the following route:

**config/routes.rb**

```ruby
get '/tasks/:id/edit', to: 'tasks#edit'
```

And in our tasks controller, add the following action:

**app/controllers/tasks_controller.rb**

```ruby
def edit
  @task = Task.find(params[:id])
end
```

Now, we need a view! This will be similar, but not exactly the same as the view we created for the `new` action. We will need to create the following HTML in `app/views/tasks/edit.html.erb`

**app/views/tasks/edit.html.erb**

```html
<form action="/tasks/<%= @task.id %>" method="post">
  <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
  <input type="hidden" name="_method" value="PATCH" />
  <p>Edit</p>
  <input type='text' name='title' value="<%= @task.title %>"/><br/>
  <textarea name='description'><%= @task.description %></textarea><br/>
  <input type='submit'/>
</form>
```

The way that we have set up this form, including the `@task.title` and `@task.description` as field values, will autofill our form with the current task information, so that a user can update one or both fields and maintain any unchanged information.

Additionally, you'll notice that there's a hidden field with a value of PATCH. So far in our Routes, we have used GET requests to retrieve data and POST requests to create data. Now we are going to use a PATCH request to update data. Normally, HTML forms only allow GET or POST requests (see more information [here](http://www.w3schools.com/tags/att_form_method.asp)). HTML won't allow us to use method='patch' in our form tag, but passing it as a hidden value gives our app the information it needs to route the request correctly.

Why do we want this to be a PATCH request? In a word, convention. Take a quick look at [this](https://www.restapitutorial.com/lessons/httpmethods.html).

### Updating a Task Resource

Now, our application will need to know what to do when someone submits the edit form, making a `PATCH` request to `/tasks/:id`; so, in our `config/routes.rb` let's add the following route:

**config/routes.rb**

```ruby
patch '/tasks/:id', to: 'tasks#update'
```

And in our tasks controller, let's add an `update` action:

**app/controllers/tasks_controller.rb**

```ruby
def update
  task = Task.find(params[:id])
  task.update({
    title: params[:title],
    description: params[:description]
    })
  task.save
  redirect_to "/tasks/#{task.id}"
end
```

There's a lot going on here - let's break it down.

Before we can make any updates to a record in our database, we first need to find that information, which is why we are using the `find` method that we used on our `show` action. Then, we can use an ActiveRecord `update` to change the object that ActiveRecord found and created for us. Finally, we need to `save` the changes we made to that object in our database. And, thinking way back to when we were creating an object, Rails will expect us to redirect after making this update, so we are going to redirect back to the tasks' show page.

Now that we have this edit functionality implemented, make sure you have your server running, and play around with your new fancy form!

## Deleting a Task

As of now, we have the Create, Read, and Update functions done - all we are missing is Delete! At a high level we need to:

- Add a button to delete a specific task
- Add a route to handle the request to delete a task
- Add a controller action to accomplish the delete

### Add a Delete Button

We don't need a form to delete a task, but we do need to know which task we want to delete, so we'll leverage a form to create a button that will send a `DELETE` request to a route with our task id.

Let's add this 'button' to our `tasks/index.html.erb`:

**app/views/tasks/index.html.erb**

```html
<h1>All Tasks</h1>

<% @tasks.each do |task| %>
  <h3><a href="/tasks/<%= task.id %>"><%= task.title %></a></h3>
  <p><%= task.description %></p>
  <a href="/tasks/<%= task.id  %>/edit">Edit</a>
  <form action="/tasks/<%= task.id %>" method="POST">
    <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
    <input type="hidden" name="_method" value="DELETE">
    <input type="submit" value="delete"/>
  </form>
<% end %>
```

Now, if you take a look at our index page at [http://localhost:3000/tasks](http://localhost:3000/tasks), you should see a 'Delete' button for each task. But, the button will throw an error - why?

### Add a Delete Route and Controller Action

When we click 'Delete' we gett an error - `No route matches [DELETE] "/tasks/1"`. So, let's go add that route to our `config/routes.rb`:

**config/routes.rb**

```ruby
delete '/tasks/:id', to: 'tasks#destroy'
```

And, with this route, we will need to add a `destroy` action to our tasks controller:

```ruby
def destroy
  Task.destroy(params[:id])
  redirect_to '/tasks'
end
```

Another new method! We have used `new`, `save`, `find`, and `update` so far - what is `destroy` doing? `destroy` is another method we are inheriting from ActiveRecord that destroys records in our database based on an id that you send to the method.

Now, we should be able to navigate to our tasks index at [http://localhost:3000/tasks](http://localhost:3000/tasks) and delete any of our tasks!

### Finished!

Congrats! You have finished your first Rails app that can handle full CRUD functionality for a database resource! We can now Create, Read, Update, and Delete tasks!\*\*

### Checks for Understanding

1. Define CRUD.
2. Define MVC.
3. What three files would you need to create/modify for a Rails application to respond to a `GET` request to `/tasks`, assuming you have a `Task` model.
4. What are params? Where do they come from?
5. Check out your routes. Why do we need two routes each for creating a new Task and editing an existing Task?
