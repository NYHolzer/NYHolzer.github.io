---
layout: post
title:      "Adding JavaScript to Your Rails App"
date:       2019-09-26 14:21:36 +0000
permalink:  adding_javascript_to_your_rails_app
---


Wait?! Why am I adding JavaScript  (here on out abbreviated as: JS) exactly? Don't I already use Rails/Ruby to view an Index and Show pages and can't I add and edit information with Create and Edit pages. So what's the point of adding JavaScript.

Great Question! Althought Rails is designed with the capability to do all of the MVC framework. The "V" or views of the MVC framework is actually better done through JS and all apps moving forward should probably favor JS to show pages. The way that this will be done is by creating APIs for our rails app that can be consumed by JS. 

Great! So now, how do we get started? 

Well, the first step is that we need to get serialized data. 

Let's add the following gem that will help us tremendously with serailziing our data into JS: 

 `gem 'active_model_serializers', '~> 0.10.10'`
 
 (You can remove the version number and see what the latest version is after installing. But be sure to add the version after you install the latest so that anyone else that needs to install these gem dependecies will have the version you used. Otherwise things may break)
 
 Now run: 
 
 `bundle install`
 
 Great. Now we can creat ActiveModel::Serializer to create serialized data.
 
 Take whatever activemodel relationships you have, will be shown. This is benefit to the `to_json` that is built in.
 
 ActiveModel::Serializer has a built in rails generator. For my app, for example, I'll use the following:
 
` rails g serializer building`

Great, now I will have a file that looks like the following: 

```
class BuildingSerializer < ActiveModel::Serializer
  attributes :id
end
```

However, I want to be able to add more attributes. Now, its been a while since I looked at this app. So to remind myself, I'll look at the `schema.rb` file to see what the table looks like. 

(TIP: Because I work on screens all day, I find it helpful to print code or similar code that I may want to use as reference so that I don't have to look at screens as often. I use an extention on vscode called [printcode](https://marketplace.visualstudio.com/items?itemName=nobuhito.printcode))

My buidlings table looks like this:

```
  create_table "buildings", force: :cascade do |t|
    t.string "name"
    t.string "address1"
    t.string "address2"
    t.string "city"
    t.string "state"
    t.string "zip_code"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end
```


So I'll add some attributes to my Building Serializer. Now, I don't know yet if I'm going to be using them. But I want to get the basics down pat and make sure that I can get JSON so I can consume it in my application. Also, my building will have many units and have many revenues and expenses through units so I will want to display this on a buildings show page through JS.

In addition to adding attributes, my `ActiveModel::Serializer` is able to understand relationships based on my model relationship and convert them appropriately into JSON data as well.

This is the final product of the BuildingSerializer: 

```
class BuildingSerializer < ActiveModel::Serializer
  attributes :id, :name, :address1, :address2, :city, :state, :zip_code

  has_many :units
end
```

Before we go any further, let's test this in the browser to make sure that everything is being JSON-fied appropriately.
In order to do this, we will go to the `BuildingsController` and add `render json: @building, status: 200` so that we can see the JSON displayed in a JavaScript array.

![JSON Example](https://i.pinimg.com/originals/03/ea/c3/03eac31dc8d3fe0416e3b5ad60ea13fa.png)

Alright so now that we have that all set. Let's add different formats to our building show page so that we can access the page through rails or javascript.

---------------




Great Resources To Help with project:
[Rails JS Study Group - Brad Smith](https://youtu.be/b93S2_Hc8z8)
