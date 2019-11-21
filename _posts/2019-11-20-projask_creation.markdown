---
layout: post
title:      "Projask Creation"
date:       2019-11-21 04:53:13 +0000
permalink:  projask_creation
---


`rails new projask --api` - this code is used to build an Rails application that will be an API server first and foremost. 
One can read more on the [rails documentation:](https://guides.rubyonrails.org/api_app.html)
This builds a smaller rails app and knows that it will be an API only.


What must we have to work with API:
Gemfile:
Activate gem-cors

How are we going to serialize our data - we can use jbuilder, activemodel serializer. We can look at other ways. - This is up to us. 

What type of generator am I going to use with Rails? 
Although scaffold generators are generally looked down upon, within an API build, they are pretty convenient and useful. Now, one has to be cautious regarding deleting resources one doesn't need. But for this project I decided to use: 

```
rails g scaffold user name username password_digest work_email gmail_email
```


Head over to the resources folder, we will want our API domain to be projask.com/api/v1/resource -- so how can we change the resources to create this type of domain?


```
  namespace :api do
    namespace :v1 do 
      resources :users
      resources :projects
      resources :tasks
    end
  end
```

Now for the API, I decided to use [Netflix's Fast JSON API](https://github.com/Netflix/fast_jsonapi): which is much faster than serializer than Active Model Serializer.
To install merely:
Add this line to your application's Gemfile:   `gem 'fast_jsonapi'`

Execute:  `$ bundle install`


> ERROR ENCOUNTER - superclass mismatch for class TasksController

So I'm working on testing my Tasks and received a "superclass mismatch for class TasksController"
![Super Mismatch Image](https://i.pinimg.com/originals/1e/f2/81/1ef281f26b5bf58728a394d40f2b196a.png)
I'm asking myself - did I create a relationship wrong? Hmm...
Finally, I was able to figure out that my controller is not properly name. 
Currently its in a folder in controllers in api in v1 so I need to write the class name as such in order to fix this issue:
```
class Api::V1::TasksController < ApplicationController
```


I'm having trouble debugging the create task on my project. I'm able to see the form submission in the fetch request action:
```
{title: "title tes", description: "desc test 11/20 "}
description: "desc test 11/20 "
title: "title tes"
__proto__: Object
Then I revise it to send to the rails backend create action:
{task: {…}}
task: {title: "title tes", description: "desc test 11/20 "}
__proto__: Object
Then I send it to the backend with this fetch request:
export const createTask = (taskData) => {
    console.log(taskData)
    const sendableTaskData = {
        task: {
            title: taskData.title,
            description: taskData.description
        }
    }
    console.log(sendableTaskData)
    return dispatch => {
        return fetch("http://localhost:3001/api/v1/tasks", {
            credentials: "include",
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify(sendableTaskData)
        })
        .then (r => r.json())
        .then (console.log)
    }
}
```
Then I am able to see in the task_params the following once I get to the task controller and I'm able to create new task with the task_params that are inputted.
```
(byebug) task_params
<ActionController::Parameters {"title"=>"title tes", "description"=>"desc test 11/20 "} permitted: true>
(byebug) Task.new(task_params) 
#<Task id: nil, title: "title tes", description: "desc test 11/20 ", assigned_by: nil, status_id: nil, dependent_task_id: nil, project_id: nil, created_at: nil, updated_at: nil>
```
However, I am receiving the following error:
```
Processing by Api::V1::TasksController#create as */*
  Parameters: {"task"=>{"title"=>"title tes", "description"=>"desc test 11/20 "}}
  User Load (0.8ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  ↳ app/controllers/application_controller.rb:6
   (0.1ms)  begin transaction
  ↳ app/controllers/api/v1/tasks_controller.rb:21
   (0.1ms)  rollback transaction
  ↳ app/controllers/api/v1/tasks_controller.rb:21
Completed 500 Internal Server Error in 21ms (ActiveRecord: 1.0ms)
ArgumentError (wrong number of arguments (given 0, expected 2)):
```
I'm a bit confused about why I'm getting an ArgumentError. When I am able to create the Task with the task_params.

Here is my task controller create action:
```
    def create 
        byebug
        @task = current_user.tasks.build(task_params)
        if @task.save
            render json: TripSerializer(@task), status: :created
        else  
            resp = {
                error_resp: @task.errors.full_message.to_sentence
            }
            render json: error_resp, status: :unprocessable_entry
        end
    end
```

RESOLUTION: I put a byebug in the task controller create action right after I set @task with the task_params. I put in the command `@task.errors` and saw that `project_id` required to be set because of the relationship that Task has with Projects. Thus, I defaulted that every task will be associated with Project 1. I will probably write in the instruction that the first project will need to be a General Tasks, Day-To-Day or whatever they want to call any generic task that can be defaulted. 

