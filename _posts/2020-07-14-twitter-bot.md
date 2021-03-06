---
layout: post
title:  The Adventures of RockyBot (1/3)
subtitle: Who you gonna Tweet?
date:   2020-07-14 16:29:53 +0100
avatar: /assets/img/RockyAvatar.png
tags: [Blog, Python]
---

---


All of my code for this project can be found on my github under the name <a href="https://github.com/SuperMairio/RockyBot"> RockyBot </a> (＾• ω •＾)


## Initial plan 

After coursework had finished for the year I suddenly found myself with an abundance of time on my hands. A considerable amount of this time was then spent sending people photos of my cat, Rocky. (I mean look at him)

<p align="center">
  <img src="/assets/img/ShoeGoblin.jpg" alt="Rocky the Shoe Goblin"/>
</p>

The problem with spamming people with these photos is that I run out very quickly and it is not the most productive use of my time. This is where I have come up with the brilliant solution to this issue: automation. Namely in the form of a bot, where I can limit the number of photos being sent a day and practice my programming.


Twitter offers the option of creating an API using the languages Python or Java. Since I have some experience using Twython, from ye olde days of high school, I first decided to try and create my bot using that. I also love python and would rather gnaw off my own leg than use Java.

The initial functionality I wanted to meet are as follows:

- Send photos at regular intervals
- Limit number of photos sent in a day
- Send photos via direct message
- Only send photos to people in list
- Automatically delete sent photos so they aren't re-sent

---

## Set-up

First I had to ensure dependencies were addressed. Normally this is as easy as running `pip install` but I was using Windows, which decided it didn't want to cooperate.
So after over an hour of Stack Overflow I did as any new developer would; I made a meme then booted into Linux.
When moving over to Linux I thought about how I would need to have my laptop on 24/7 and how impractical that would be, mainly for me trying to sleep with the fans trying to achieve mach one.
The alternative I went for was to use a Raspberry Pi which I could leave running indefinitely and not worry about sound or anything melting through my desk.

<p align="center">
  <img src="/assets/img/meme.jpg" alt="Unfunny meme about importing twython" />
</p>

Originally I had envisioned myself only requiring two .py files. The first to hold the variables that contained sensitive data and the second to contain all the actual code.
To ensure that the file holding all the API keys would not be included in my GitHib repo, beyond existing with example data, I used the command: 

`git update-index --skip-worktree topsecretfile.py `  

To ensure photos are sent at set intervals I originally envisioned a script that would be running continuously and using some form of counter functionality. After actually considering the problem for more than ten seconds I realised that this would not be ideal. Instead I opted to set up a cron job to run the script at set times.

The timings I eventually settled on was every to run the script on every sixth hour, (thank you <a href="https://crontab.guru/#0_*/6_*_*_*">crontab guru</a>!) the command I used is as follows:

`sudo echo "0 */6 * * * /usr/bin/python3 /home/mai/Documents/Twitter\ Bot/TwitterBot.py" > /var/spool/cron/mai` 

(Before you say anything, i'm not silly enough to set up a cron job for a script that hasn't even been written. At this stage I figured out how they work and got the command ready for when the Python code was done)

Using cron means that I do not have to keep a persistent connection with Twitter or have a script running constantly in the background of my computer. Another benefit to all of this is that only four images are being sent a day, which is beneficial as Twitter rate limits requests via bots to avoid someone setting up a DOS attack.

Just after I had set-up the cron job I went onto <a href="https://developer.twitter.com/en"> Twitter's Developers page </a> to generate some API keys for my bot. Upon attempting to get these strings I discovered that since 2016 Twitter has drastically increased it's requirements for them. I had to sit and write a couple of paragraphs with why I wanted to make a bot and then send it off to them for review.
<p align="center">
  <img src="/assets/img/twitteressay1.png" alt="Twitter asking me for 200 characters describing my bot" />
</p>


---

## Programming

Whilst waiting on a reply from the Twitter gods I started on coding.

Since my Rocky photo collection is on my computer I used the python <a href="https://docs.python.org/3.6/library/os.html"> os module </a> to iterate through the folder and grab the first image. Knowing that my picture pool was not infinite I decided that rather than delete the images that had been sent to instead move them to a 'Oldies but goldies' folder. I used try except to check if there were any photos left and if not to take one from the used photos folder.

<p align="center">
  <img src="/assets/img/GetImage.PNG" alt="Screenshot of my Get_Image function" />
</p>

Although I originally planned to use two files for all of my code this changed when I decided to use a SQLite database to store the usernames. The reason I chose a database over using an array is because I wanted to revisit my SQLite knowledge. I decided to also store privilege levels, alongside the usernames. These would be where certain users would also receive additional information such as error messages, how many photos were left etc. 

The separate .py file for my database code was because I wanted to create a database helper where the user has options to easily add/remove/edit data. As the sending of photos was to be automated I didn't want to have to take a user input every six hours to check that they wanted to just send a photo. I am aware of options for this to be done via the command line but for consistency and ease I decided to create a python script. 

<p align="center">
  <img src="/assets/img/DBOptions.PNG" alt="Screenshot of my database options" />
</p>

In TwitterBot.py I store the username and privlege level query results in a nested list so that it's easier to return. I chose to do it this way because I like compartmentalising my code into functions in an almost object-orientated approach. I find this makes debugging easier as if something breaks I only have to fix a function. 

To send the photo I use the send_direct_message Twython function and load in the username and image. One thing I noticed when <a href="https://developer.twitter.com/en/docs/direct-messages/sending-and-receiving/guides/direct-message-migration">looking online </a>is that this feature is depricated in the current version of Twitter and a POST method should be used instead. As will be mentioned below I did not update this line due to my project recieving an unfortunate setback. 
Once an image is sent I use the Python <a href="https://docs.python.org/3/library/shutil.html"> shutil library </a> to move said image into the used photo folder so that I don't accidently resend it in six hours. I'm aware of the os.rename function but I decided to use shutil as it required less parameters and I didn't want to change the name of my photos.

<p align="center">
  <img src="/assets/img/SendandGetPics.PNG" alt="Screenshot of my database options" />
</p>

The reason that I did not update this code or implement any functionality for the admin users was because I got some emails back from Twitter.

---

## Twitter 

A few days after sending off my application Twitter sent me an email, asking for all of the same information but now in email form. This was annoying but I was very excited about showing everyone my cutest collection of cat photos so  I wrote up and sent off another explanation of what my project was, this time including the link to my GitHub repo. A few days after that I got another email. 

RockyBot violates one or more of Twitter's rules.

After asking for clarification I received the same email just with a very passive agressive emboldened Automation Rules link. Unfortunately I still have no clue what rules my cat photo sharing system broke and I wasn't willing to haggle my functionality with them.

<p align="center">
  <img src="/assets/img/no1.PNG" alt="First rejection email" />
</p>

<p align="center">
  <img src="/assets/img/no2.PNG" alt="Second rejection email" />
</p>

As a result of them rejecting my request for API keys I am unable to test my code and so I have no idea if it even works. If you are upset that you cannot be sent Rocky pics every six hours fret not for I have decided to move over to Discord instead and will be creating a second part blog for the adventures of RockyBot.

---


## References
<details>
 <summary markdown="span"></summary>
Crontab guru: <a href="https://crontab.guru"> https://crontab.guru </a> <br/>

Twitter Developers: <a href="https://developer.twitter.com/en">https://developer.twitter.com/en</a> <br/>

Python os documentation: <a href="https://docs.python.org/3.6/library/os.html">https://docs.python.org/3.6/library/os.html</a> <br/>

Python shutil documentation: <a href="https://docs.python.org/3/library/shutil.html">https://docs.python.org/3/library/shutil.html</a> <br/>

Twitter's rules: <a href="https://developer.twitter.com/en/developer-terms/agreement-and-policy"> Developer Agreement and Policy </a>, <a href="https://help.twitter.com/en/rules-and-policies/twitter-automation"> Automation Rules </a> & <a href="https://help.twitter.com/en/rules-and-policies/twitter-rules"> General rules</a>
</details>
