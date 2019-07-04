---
layout: post
title:      "Rails: Has Many Through  Association Lab"
date:       2019-07-04 16:55:47 +0000
permalink:  rails_has_many_through_association_lab
---


Today is July 4th. There is no support to help me through this lab. So I've decided to go public with my struggles and take this from the top and walk through my thought process with you for this lab.

Objective: This lab is testing all our learning until now in some ways. From the readme let's go through each segment and strategize what will be the best course of action. 

> Step 0: Create all of the migrations, models, routes, controllers, and views for this lab.> 

Oh no! All those migratiosn and models we will need to create from scratch?? This is going to take hours!
Well, not so fast. We have a secret weapon to help us setup all of this - Rails Generators. These generators give us the ability to setup the basics quickly that's consistent across all our apps. In addition, because most of the setup is automated, there are less typos and syntax errors. It also follows Rails best practices: RESTful naming patterns, removes duplicate code, partials. HOWEVER, we need to be cautious and choose the right type of generator for our needs. Generators can create a lot of garbage code that we don't need. We can sometimes waste hours deleting code instead of spending that time creating it the way we want it to begin with.

Different types of Rails Generators:
1. Migrations
Example: 
```
rails g migration add_published_status_to_posts published_status:string  --no-test-framework
```

This will automatically generate for us migration file with timestamp. In addition, for add and removekeywords in the beginning of the migration file name, the generator will also add the appropriate change column line.

Example: 
```
#db/migrate/20190704092931_add_published_status_to_posts

class AddPublishedStatusToPosts < ActiveRecord::Migration
      def change 
           add_column :posts, :published_status, :string 
       end 
end 
```

2. Models
This is used often as it helps us create the models and associated database table without adding a lot garbage. 
So this will create database migration (like the migration generator) and in addition the model file that inherits automatically from `ActiveRecord::Base` 

3. Controllers 
Great for: 
(1) static view 
(2) non-CRUD related features.

We will be able to create the following in one quick swoop: 
* Controller file that inherits from `ApplicationController`
* set of routes to each of the generator arguments
* new directory of all view templates along with  controller actions for each
* view helper method file
* Coffeescript file for specific JavaScripts for that controller
*  `scss` file for the styles for the controller

4. Resources
Great for: CRUD routes 

Will magically create the following: 
* migration file that will create new database table for attributes passed to in the generator
* model file that inheris from `ActiveRecord::Base`
* controller file that inherits from `ApplicationController` 
* view directory, but no view template files
* view helper file
* Coffescript file for specific JavaScripts for that controller
*  `scss` file for the styles for the controller
*  full resources call in the `routes.rb` file

5. Scaffold
it's best practice not to use these. So I won't even go through it but officially you can setup a complete application in 1 swoop with this magical device. But will prodcut a lot of extra code that is not needed.

Hmm... Looking over the specs, it seems like we will only need to create index and show pages. 
But these will be CRUD related. 

I say let's utilize the Rails Resource Generator so that all of this stuff is setup for us. It doesn't create view files so we will be able to customize it the way we like it. 
Here is a fellow flatiron-student's blog post regarding resource generators: [Ruby on Rails Resource Generator: Instant Full-Stack MVC Framework (CRUD not included)](http://https://medium.com/@yongnicholasakim/ruby-on-rails-resource-generator-instant-full-stack-mvc-framework-crud-not-included-4967b4d3e3bb)

Let's go ahead and implement this for the lab.

But wait?!

What are our tables going to look like? 

![table](https://i.pinimg.com/originals/44/83/e2/4483e269d590324b9a58f605a53e8bfc.png)

The real first instruction is the following: 

> Step 1. Create migrations and models for doctor, patient, and appointment. Because this is for a hospital, a doctor can have many patients, but a patient can also have many doctors. Patients and doctors both have many appointments. Our appointments table should have a column called appointment_datetime to represent the date and time the appointment will take place. (When you display this datetime, make sure it's in a human readable format. The tests will be expecting "January 12, 2016 at 3:00".)

First let's setup up our migrations -- started with the following generator: 
```
rails g resource Doctor name:string department:string --no-test-framework

rails g resource Patient name:string age:integer --no-test-framework

rails g resource Appointment datetime:datetime patient_id:integer doctor_id:integer --no-test-framework
```

Notice that I added `--no-test-framework` so that I don't mess with learn's pre-set tests.

Next, routes. We'll just use the easy resources which will auto-generate. 
However, we only want a show-page for appointments. We don't anyone seeing everything else. 
So we have the following for the resources which was arleady coded for us before we started the generators: 

```
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  resources :doctors
  resources :patients
  resources :appointments, only: [:show]
end
```

Next, the associations:
Here is how they will look: 
```
class Appointment < ApplicationRecord
    belongs_to :patient
    belong_to :doctor
end

class Doctor < ApplicationRecord
    has_many :appointments
    has_many :patients, through: :appointments
end

class Patient < ApplicationRecord
    has_many :appointments
    has_many :doctors, through: :appointments
end
```


Going in order of the specs: 

> Create an Appointment#show page that shows the date, time, patient, and doctor for that particular appointment. Note that there should NOT be an Appointment#index page –– we don't want to have all of the appointments on display for anyone to see.

So this is how my code looked: 

```

#app/models/appointment.rb

class Appointment < ApplicationRecord
    belongs_to :patient
    belongs_to :doctor

    def doctor_name
        self.doctor.name
    end

    def patient_name
        self.patient.name
    end
end

#app/controllers/appointment_controller.rb

class AppointmentsController < ApplicationController
    def show 
        @appointment = Appointment.find(params[:id])
    end
end


#app/views/appointments/show.html.erb

<h1> Appointment </h1>
<label>Doctor:</label> 
<%=link_to @appointment.doctor_name, doctor_path(@appointment.doctor)%>
<br>
<label>Patient:</label>
<%=link_to @appointment.patient_name, patient_path(@appointment.patient)%>

```

To explain: 
This code uses the [Law of Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter) which is a design principle for object-oriented programming that have the following rules:
1.  Each unit should have only limited knowledge about other units: only units “closely” related to the current unit.
2.  Each unit should only talk to its friends; don’t talk to strangers.
3.  Only talk to your immediate friends.

In practicality, what does this all mean?

Well, in regards to *views*, the rule can be summed up as follows: **Use only one dot rule.**

![Example of only one dot rule](https://i.pinimg.com/originals/b4/17/c5/b417c5908db3abc54334d254ea0047eb.png)

Once we are on what should go into views and what type of code - here are 3 rules that I found from [Paul Keen of JetThoughts](http://jtway.co/cleaning-up-your-rails-views-with-view-objects-42cf048ea491) that go along with what we have learned in Flatiron School:
* Use only one dot rule. Avoid expressions that are accessible with more than 1 dot.
* Don't hit database in views. This is obvious but very common. You should not make database calls inside views.
* Avoid the variables assignment inside views. They should display already computated values. (models are great for these)

Therefore, we put grabbing the name of the doctor and patient into their own methods in the models section. 
We were then able to use these methods in the views to display the name.

> Create a Doctor#show page that displays the doctor's name, department, and appointments, with each appointment's date, time, and patient name (linking to the patient's show page).

The first spec for this is as follows: 
```
doctors
  #show page
    shows all of a doctor's appointment times in a human readable format (FAILED - 1)

Failures:

  1) doctors #show page shows all of a doctor's appointment times in a human readable format
     Failure/Error: expect(page).to have_content("January 11, 2016 at 20:20")
       expected to find text "January 11, 2016 at 20:20" in ""
     # ./spec/features/doctors_spec.rb:13:in `block (3 levels) in <top (required)>'
```

Hmm... 
So having data in our seed file and running `rake db:seed` I was able to run `rails console` to see what type of data is stored in the `appointment_datetime` .
We get the following: 
```
#<Appointment id: 1, appointment_datetime: "2016-03-15 18:00:00", patient_id: 11, doctor_id: 12, created_at: "2019-07-04 14:59:39", updated_at: "2019-07-04 14:59:39">
```

Hmm.. How do I get from this format: `2016-03-15 18:00:00` to this desired view format: `January 11, 2016 at 20:20`

Guess what?!

There's an AWESOME METHOD for this!!

We can use `to_formatted_s(format = :default)` *public*
Find more infomartion on [APIDock](http://apidock.com/rails/DateTime/to_formatted_s)

So if we do `a1.appointment_datetime.to_formatted_s(:long)` we can convert the standard: `Tue, 15 Mar 2016 18:00:00 UTC +00:00` to the following: `March 15, 2016 18:00`

Well, wait a minute! What about the "at" in between the year and time.

Remembering my Ruby days, I quickly google "ruby break apart by space" and find an article [Splitting Strings in Ruby Using the String#split Method](http://www.thoughtco.com/splitting-strings-2908301)
Or if I want to be fancier: [Ruby Docs on this](https://ruby-doc.org/core-2.6.3/String.html#method-i-split)

That's right, I can use the split method. By default, it will split contents by the single space.
```
a1.appointment_datetime.to_formatted_s(:long).split
 => ["March", "15,", "2016", "18:00"]
```

Now we are getting places. We chopped this into 4 pieces. Now to add something to an array, we would use the `#insert` method. See [Ruby Docs](https://ruby-doc.org/core-2.6.3/Array.html#method-i-insert)

```
a1.appointment_datetime.to_formatted_s(:long).split.insert(3, "at")
 => ["March", "15,", "2016", "at", "18:00"]
```

Then finally we will use the `#join` method to convert the array into a string: 

```
a1.appointment_datetime.to_formatted_s(:long).split.insert(3, "at").join(" ")
 => "March 15, 2016 at 18:00"
```

Perfect!

*(Note: All of these methods I was able to practice in my rails console.)*


Put all my logic into the Appointment Model so it now looks like this: 
```
class Appointment < ApplicationRecord
    belongs_to :patient
    belongs_to :doctor

    def doctor_name
        self.doctor.name
    end

    def patient_name
        self.patient.name
    end

    # => "December 04, 2007 00:00"
    def convert_datetime_to_long_format
        self.appointment_datetime.to_formatted_s(:long)
    end

    def convert_datetime_to_readable
        self.convert_datetime_to_long_format.split.insert(3,"at").join(" ")
    end

end
```

The Doctor view now looks like this: 
```
<h2>Doctor:<%=@doctor.name%></h2> 
<br>
<label>Appointment Time</label> <br>
<%@doctor.appointments.each do |a|%>
    <%=a.convert_datetime_to_readable%> 
    <%=link_to a.patient_name, patient_path(a.patient)%> <br>
<% end %>
```

From here. I was able to complete the lab without much lookup.
So I'm going to end this blog post here and hopes that this may help other students or perhaps be a good review of some important rails concepts. 

You can find my full source code here: https://github.com/NYHolzer/displaying-has-many-through-rails-lab-online-web-pt-011419

Happy Coding!
