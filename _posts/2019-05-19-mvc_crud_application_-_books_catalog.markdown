---
layout: post
title:      "MVC CRUD Application - Books Catalog"
date:       2019-05-19 09:16:06 -0400
permalink:  mvc_crud_application_-_books_catalog
---


Good Morning Codeaholics! (Note: Codeaholics was the nickname for my cohort at Flatiron School Part-Time)

I'm coming to a close on my MVC Application App and I'd love to share with yousome helpful tips and guidance that I've learned along the way to perhaps help others in their Sinatra Applications.

> How did you set up your local environment?

Great question. I'm using a Windows 10 64-bit machine. 

I want to give a shout out to Sim Greenbaum who sent me two articles that brought clarity to how to set this up: 
https://github.com/micahshute/wsl-setup - This blog post gives you the Step by Step that I followed to setup the local environment
https://gbs4ever.github.io/wsl_-windows_subsystem_for_linux - This blog post gives you some background into the WSL - Windows Subsystem for Linux

Just in case those links don't work when your seeing this blog or you just want a one stop shop, here is summary of what to do: 

*First step is to enable Windows Subsystem Linux (WSL) on windows*
1. Go to Windows Search bar and type in `turn windows features on or off`
2. Click on the result that matches this query and a window will pop up (from the Control Panel)
(Alternatively, Go to Control Panel, Click on `Program and Features` then on the left side click on `Turn Windows features on or off` -- this will take you to the same place)
3. Scroll down to `Windows Subsystem for Linux` ensure its checked!
4. Restart your computer 


5.  Go to Microsoft Store - search for Ubuntu and download (if not already installed)
6.  Open Ubuntu
7.  The first time will prompt you for a user name and password

Note: One thing those articles don't tell you Ubuntu will prompt you for a username and password for Linux. The password you type in will not show up on your screen. It will look like nothing is happening but in reality this is a security feature so that no one can see or steal your password. It will prompt you for the password type so if you feel you mistyped it the first time it will be a mismatch the second time and let you restart that password. 

*Now its time to outfit our linux subsystem with lots of goodies*
8.  You will type in the following commands into the Ubuntu app.
Note: After copying the text, just right clicking into the Ubuntu app pasted the code - CTRL + V does not work. Just right click after copying and wolla it should jsut paste in. Then click enter to run it.
9.  Download dependencies: 
```
sudo apt-get update
```
```
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev libpq-dev libgdbm-dev libncurses5-dev automake libtool bison gnupg postgresql postgresql-contrib
```
10.  Install RVM
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
```
11.  Then run the following: 
```
\curl -sSL https://get.rvm.io | bash -s stable --ruby
```
12.  Then finally: 
```
source ~/.rvm/scripts/rvm
```

13.  Next step is to install Ruby - Please note that I've already installed Ruby on my desktop before starting this process - you can do that here: https://rubyinstaller.org/  -- If you did so  then the previous scripts should have found the ruby installed and connected it already. You can read through the output and see what happened. But if not, it doesn't hurt to run the following code: 
```
rvm install 2.6.1
rvm use 2.6.1 --default
```

14. Install the Bundler Gem 
```
gem install bundler
```

15.  Let's setup git
```
git config --global color.ui true
```

16.  For the next few commands, input your specific information - "your_name" can be your username or whatever and "your_email" - I would use the ones connected with the github account (but I don't know if it matters). I believe when you make udpates to github it will show whatever you put here: 

```
git config --global user.name "your_name"
```

```
git config --global user.email "your_email@example.com"
```

Note: This will work for HTTPS. If you want to setup the SSH then check out this link: https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

17.  Let's get Learn's gem setup. This will allow you to use commands such as `learn`, `learn submit`, `learn open` like you've probably been using the learn IDE (if your a flatiron student -- I'll add a plug that after doing substantial research, Flatiron School is by far the best in class  - go now: http://flatironschool.com/ -- They have part-time programs to meet your busy schedule) 

```
gem install learn-co
```

18. Now in order for learn.co to know who you are - Navigate in your browser to learn.co/your_github_username , At the bottom of the page you will see an Authorization Token - you will need to copy an paste this soon.
```
learn whoami
```

When it prompts you, copy and paste that Auth Token. 

19. By the way, here is an awesome post regarding learn commands: https://help.learn.co/technical-support/troubleshooting-tips/learn-commands

20. Let's install NodeJS
```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
```

```
sudo apt-get install -y nodejs
```
21.  Then Ruby on Rails: 
```
gem install rails
```

*Now let's setup some our text editor - VS Code is super popular and highly recommended*
22. Download VS code here: https://code.visualstudio.com/ -- Hit that big Download for Windows button.
23. To configure the Ubuntu Terminal to work within Visual Studio code follow the instructions below: 
* Open VS code
* Open the command palette - `F1` or `CTRL` + `SHIFT` + `P`
* Type into the drop down search bar that just popped up the following: `terminal: select default shell`
* Then click on WSL Bash (This will set you default terminal to WSL Terminal that we just setup in Ubuntu)

You are all setup now! Good luck!

> How did you get started? I also heard you were behind - what were the essentials?

This lecture by Avi Flombaum really helped me get started: https://youtu.be/_S1s6R-_wYc 

He takes you step by step on how to setup a useful login. He starts from the ground up. 
You may prefer to start by using the Corneal gem which is supposed to make the process extremely simple by setting up most of the folders.  Here is a video on that: https://youtu.be/uAKYte1ngnk

One helpful tip: 
In the ApplicationController, I setup my helpers as such based on the video above which proved to be extremely useful to making my code coherent and KISS (Keep It Simple Stupid): 

```
    helpers do 
        def logged_in? 
            !!current_user 
        end

        def current_user 
            @current_user ||= User.find_by(:email => session[:email]) if session[:email]
        end

        def login(email, password) 
            #Is the user who they claim to be
            #check if a user with this email actually exists
            #if so, set the session
            user = User.find_by(:email => email) 
            if user && user.authenticate(password) #if statement assignment
                session[:email] = user.email
            else 
                redirect '/login'
            end
        end

        def logout 
            session.clear
            redirect '/login'
            #Emailing them letting them know that they logged out
        end
    end
```

With this code, you can see how simple it is to check if a user is logged_in? before pages that only users can get to if logged in. You can also use the @current_user to easily grab the current user to only show them their content. 

Here is my code in my books_controller which made extensive use of the helpers :-): 

```
class BooksController < ApplicationController
    get '/books' do 
        if !logged_in?
            redirect '/login'  #Redirecting if they aren't
        else
            @current_user
            @book = Book.new
            erb :"books/index.html" 
        end
    end

    get '/books/new' do 
        # checking if they are logged in
        if !logged_in?
            redirect '/login'  #Redirecting if they aren't
        else 
            erb :"books/new.html"   
        end
    end

    get '/books/:id/edit' do 
        if !logged_in?
            redirect '/login'  
        else 
            @book = Book.find(params[:id])
            erb :'books/edit.html'
        end
    end

    get '/books/:id' do 
        if !logged_in?
            redirect '/login'  #Redirecting if they aren't
        else 
            @book = Book.find(params[:id])
            erb :'books/show.html'
        end
    end

    post "/books/search" do 
        @books = Book.all 
        @user = current_user
        if params[:search]
            @books = Book.search(params[:search])
        else  
            @books = Book.all
        end
        erb :"books/results.html"
    end

    post "/books" do 
        @book = Book.new 
        @book.title = params[:title]
        @book.description = params[:description]
        @book.user_id = current_user.id 
        @book.save

        redirect "/books"
    end 

    patch '/books/:id' do 
        @book = Book.find(params[:id])
        @book.title = params[:title]
        @book.description = params[:description]
        @book.save 
        @book 

        redirect "/books/#{@book.id}"
    end

    delete '/books/:id' do 
        @book = Book.find(params[:id])
        @book.destroy
        
        redirect '/books'
    end


end
```

Now, if the Sinatra ActiveRecord CRUD lesson was extremely helpful in setting up the rest of the application. This was my completed lesson with answers: 
https://github.com/NYHolzer/sinatra-ar-crud-lab-online-web-pt-011419

> How did you add search capabilities to your app?


