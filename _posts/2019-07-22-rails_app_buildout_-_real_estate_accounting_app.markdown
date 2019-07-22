---
layout: post
title:      "Rails App Buildout - Real Estate Accounting App"
date:       2019-07-22 04:24:34 +0000
permalink:  rails_app_buildout_-_real_estate_accounting_app
---


7/13/2019 - Start Day

So I'm behind but I'm not gonna let that stop me from creating this app.
Aysan is giving us amazing videos and guidance that I think I can learn and code while I creat this App and finish up the lessons. Time management at its maximum. It's also not so easy this past week (1st week of Project Week) as I was filling in for a coworker who went on a honeymoon so doing double duty at work and having to think about project and catchup is no fun.

Well, don't worry. I'm going to log all of what I learn along the way and the questions I have and what I've discovered long this week-long project journey.

First thing's first. Let's start a new rails app: 

```
rails new realestacct
```

Next, let's connect our github to this local file:
1. `git init` - this will initialize our local directory
2. `git add . ` - add all of our folders that we have on our local
3. `git commit -m "first commit"` - 

I had some trouble with SSH, VSCode and github so I just went the HTTPS route of connecting a local to remote.

7/14/2019
> Adding a Login

To add my User stuff:
```
rails g resource User username:string email:string password_digest:string --no-test-framework
```

Ensured my user model contains `has_secure_password`

Next made sure my Gemfile included `bcrypt` - I had to uncomment it out and run `bundle` again.

Next, I watched an amazing video by Jennifer Hansen who explained how to setup a login / logout etc in Rails: 
[Rails Project Prep: Starting your Rails Project](https://www.youtube.com/watch?v=22N5TQxW6oA&feature=youtu.be)

7/16/2019
> What if the name for my generator is more than one word?

This article was very helpful in defining the rails naming conventions: [Rails Generator Naming Conventions](https://medium.com/@decode2018/rails-generator-naming-conventions-c3f7df937033)

Our property manager resource will be generated written in CamelCase.

7/17/2019
On a previous occation, I discussed my database structure with my cohort lead.
![Database Table for Real Estate App](https://i.pinimg.com/originals/8a/01/77/8a017737bdcbc83404df3bdc73164d03.png)

Tonight, first thing I'm going to use the two rails resource generator to create two of my objects - buildings and units.

 `rails g resource Building name:string address:string city:string state:string zip_code:string` 
 `rails g resource Unit apt_num:string tenant:string building_id:integer property_manager:integer`
 
It's time to start wiring things up:

```
class Unit < ApplicationRecord
    belongs_to :user
    belongs_to :building
end
```
```
class Building < ApplicationRecord
    has_many :units
    has_many :users, through: :units
end
```

```
class User < ApplicationRecord
    has_secure_password #password validations
    
    has_many :units
    has_many :buildings, through: :units
```
 
 7/21/2019 - Sunday - Last Day of Project
  Today I have a lot to add to this project for the requirements: 
	
	1. Nested Routes
	2. OmniAuth
	3. ActiveRecord Scope Method

To begin, last night I reviewed the two readme code-along lessons on nested resources so that I can create a nested resource for creating a unit from building show page and editing units from building show page. 

Here are some issues I ran into and how I figured them out: 

1) When adding `new_building_unit_path` to the buildings show page. I was getting an error that the `:building_id` couldn't be found. 
Looking at `rake routes` this seemed to be the correct path. 
However, I realized that I need to add `@building` so it know which building to nest the path under.
So it should really look like: `new_building_unit_path(@building)`. 

2) OmniAuth - Wasn't logging in correctly. Realized afterwards that I already had an email in with a different password. Therefore, it wasn't logging in successfully.

I may update this post at a later date with more stuff. But this is the wrap for now.




 
