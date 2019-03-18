---
layout: post
title:      "The CLI Project Journey"
date:       2019-03-18 03:21:21 +0000
permalink:  the_cli_project_journey
---


Learning on the Online Part-Time Flatiron Software Engineering program has been such a wonderful journey only two months in. After learning Procedural and Object Oriented Ruby, we were able to use what we have learned to create our first program.  The applicable possibilities were endless but what to choose and how to choose were on the table.

The CLI Project requires scrapping a website to obtain data that would be accessible via a Command Line Interface (CLI). The CLI is a way of communicating with our program via text. I guess one can think of CLI as texting with our computer. Our CLI program texts messages to us and we text messages back. No emojies though :-) 

**CHOOSING WHAT TO SCRAP**
At the start of the program, it was recommended by Jenn Leigh Hansen to first confirm that the website you desire to scrap is scrappable. (See video here: https://youtu.be/a1AoIpkiado)
First question: Does it load with HTML or Javascript? We want the website to be HTML based. (JLH)
Second question: How complex is the site? Is this easy enough to figure out in two weeks time (My question)

The first question was interesting because when I was thinking about the website I had no idea that some were nto HTML based. So I quickly looked into whether the site was HTML or Not. 

At work, being an e-commerce company, we always need data from Amazon. Amazon doesn't always provide all the data we need from the API or backend. So, at first, I was thinking what if I can scrap Amazon for data? Can I search products through my program and gather the data of the products into Classes. Then I can analyze the data of the classes and spit out a CSV file with the findings. 

Well, Amazon was very daunting. First, I ran into a 503 error. I couldn't even get to square one. That was my first lesson, Amazon requires a user-agent. A User-Agent basically tells the website who you are. I found my user-agent here: (https://www.whoishostingthis.com/tools/user-agent/). Then I found that each category has a different way of displaying the data. This will cause issues when scrapping the HTML using the CSS as the CSS changes depending on your search term. So I figured out I would need to search on a category basis. Thus, this would be a bigger project then I initially anticipated. 

Back to figuring out what to scrap and what type of program to make. I turn to my wife and she suggests that I do recipe finder scrapper. She shows me allrecipes.com, a common website she searched for recipes, and I find that it has HTML and its an easy format to scrap. Yay! Found a website with all the benefits. 

**SETTING UP YOUR ENVIRONMENT**
Wonderful. I found a website. Now, where to code. Flatiron School recommends using the Learn IDE but at the time of the project the Browser IDE was acting a bit funky so I decided to try a local environment. I decided on atom - its free and was fairly simple to setup. 
I was working on Windows 10  x64 desktop computer.
Here are the steps if your interested:
1. Go to https://atom.io/ and hit Download button for the latest version
2. Atom comes with Ruby ready to go and Git and Github access
3. Install Ruby and Git on your computer if you haven't done so already. Here: https://rubyinstaller.org/ and Here: https://gitforwindows.org/
4. After installing git you will want to tell git who you are by typing the following commands:
```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```
5. In the prompt for Git it will allow you to choose atom as your editor.
6. CTRL + SHIFT + P --> To open the search in Atom -- search clone -- This will get you to box to throw in the site address for your repository
7. Will save on your computer and open in Atom
8. Now on the bottom left you will see a Github button and you can use that to open the Git and Github features. You tab to the Git tab to stage and commit changes.
9. On the bottom left, you will see a Push after you commit changes -- this will push change you made and committed to the Github repository on your github account.

There is also a helpful blog post by silhouette which is very good fore setup on learn IDE which I wish I could have been shown earlier: https://sillhouette.github.io/crowder_news_data_gem

**CODING**
Watching Avi's video really helped in learning how to install Bundler and setting up one's environment. He recommeded to setup the CLI and the user interface first. I ended up doing this last because I decided to focus on scrapping the website which was the most challenging for me at the time. However, in retrospect, it would have probably been better to watch Avi's entire video. The CLI can sometimes take up more time setting up when you need to debug both the scrapper and the CLI.

Here was my thought process and how I built the program:
1. What data do I want the user to see? 
2. Build Classes to store that data.
3. Figure out how to scrap that data from the website chosen
4. Test everything! -- 'pry' is your best friend
5. build CLI -- how user to interact? 

Throughout my coding journey for this project, I had to switch between different websites. 

My first website was www.allrecipes.com but I eventually got locked out since I scrapped too many times.
Then I switched to www.kosher.com but for some reason my scrapping failed on that site after a few days.
I kept my scrapping data from www.allrecipes.com so I was to easily switch back to this site once I found out I was allowed back on. Yay!

But because I built my code with adaptability, I was able to switch and navigate these bumps a lot easier.
1st - I created a hash for the 3 different websites I need. One for Appetizers, one for Main Dishes, one for Desserts. Wehn one website didn't work, it was fine because all I had to do was switch the hash values. The site for Nokogiri was in a site instance variable and each website could be called upon by defining the site instance variable to the websites hash keys. 

2nd - I tried to ensure that each function was separated. I didn't realize how important this was until I had to change things around. Anyhow, for example in my scrapper.rb, I created a separate method for getting the website page into Nokogiri HTML, a separate method for getting the recipes html array. Then I scrapped the recipes in another method while adding them to my recipe class. After receiving the recipe titles, descriptions, and links, I used the links of each recipe to scrap the ingredients and directions in yet another method. I found that this made it very simple to see what was going on and how it was being scrapped.

SCRAPPING
Besides for the learn lessons, this was also helpful: http://ruby.bastardsbook.com/chapters/html-parsing/
Scrapping really needs to be learned by just doing it. 
I recommend doing some Nokogiri chopping on your website, storing it in a variable, and adding a 'pry' to play around with. 
NOTE: Don't forget to ```require 'pry'``` at the top of your document. Also, don't forget to install the pry gem if it is not already there. 

Also, call the method you are using to use Nokogiri such as #get_page method or whatever you choose to call it. (You can see the code I used to play around with my site.) Then play around with the variable adding different css elements. Once you think you got further into it, do .css( )[0] -- Take the first of the css array. I literally had no idea how to scrap until I got to this project. Practicing and playing around really helped me master the scrapping concept and method. NOTE: The chrome inspect feature is an essential tool and I would say is a must have.
```
class Scrapper
  def get_page
    doc = Nokogiri::HTML(open("https://www.allrecipes.com/recipes/76/appetizers-and-snacks/"))
		binding.pry
  end
end
Scrapper.new.get_page
```

There is so much more to write about but these are some of the things that I came across while working on this project and hope some of these details may help you as you start your journey on the CLI Project. Feel free to reach out to me and I'll be happy to help in any way. I can be reached at nissan.holzer@gmail.com -- Put "Flatiron Student - CLI Project" in the subject and I'll be sure to prioritize your emails. Happy coding!


