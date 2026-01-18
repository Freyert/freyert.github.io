---
layout: default
author: Fulton Byrne
title: "2026 Portfolio Project: Local Playlist" 
---

I wanted a little portfolio project to start the New Year off. Something to expand my skill sets, but also to solve a problem I have (so I would stay interested).
My problem: I love listening to music, but rarely go to live shows with friends. The key barrier I identified was that all of the local venues had artists I didn't recognize.
Therefore, I should write a tool that discovers music playing locally and creates a playlist for me to listen to. This way I know who is playing nearby this year and who I am interested in seeing live.

# How to use AI?

I must confess I mainly wanted to develop more AI assisted coding skills as part of this project. Previously I built an iOS application for tracking my workouts. I eventually dropped developing this
application for a spreadsheet because the AI tools could never solve some basic bugs and the code base was so foreign to me I would prefer starting from scratch. A spreadsheet was a passable way to 
continue and still develop a useful skill (spreadsheets).

## Trying AI again
I heard that the models improved dramatically since my last major swing at a fully AI coded application. I imagined writing my idea out in a design document, passing this to the AI, walking away, and coming back to what I wanted.
Not the case. Something came out that worked(?), but it was more of a bash script written in TypeScript than a well thought out program with a vision to being something more.

Additionally, it seems as the models have quote, unquote _improved_ their costs rose and rate limits incurred. My $20/month Claude subscription had been fine to write an iOS app with multiple views in the past, but now
I could barely get out 100 lines of TypeScript without hitting the limit. I imagine there was a lot of token consumption as it helped me research, but honestly I just got tired letting the toy take the lead.

## AI has its place
Certainly I am not _abandoning_ AI altogether, but I need to recognize where it is useful. I see others, some of whom I think are trustworthy, giddy at the results they achieve with letting agents run free. For now I think I will
focus on using them to assist me as I code. Additionally, I think there are some very cool ways to include AI in my application's pipeline; which I will discuss later.


# Session One Accomplishments

## Researching the problem

### How to get data

AI helped tremendously here. All I needed to do was frame the problem as so: "I want to find musical events local to me. Can you research some local venues to see if they have an RSS feed?". I think in some ways my framing was too leading on particular solutions.
However, the AI assistant was able to search several venues, identify that some had RSS feeds, and some did not. When I investigated a few RSS feeds it revealed that there was no standard data schema across venues and the feed was just HTML.

I believe scraping the sites will be excellent for an AI in the future. Perhaps I can use an expensive model to label a data set, then use the data set to fine tune a smaller model to efficiently translate scraped, unstructured data into structured data.
Until then I will pull data by hand.

### Finding a music service API

The AI researcher went in absolute circles on this one. Multiple researchers believed that Spotify _still_ maintains an API I could obtain access to. Not the case unfortunately. Fortunately, Apple Music maintains an API. After selecting Apple Music as my 
music data source, I spent so much time trying to learn how to use their API. The AI returned to my good graces here as it could help reason about the novel systems for authenticating to the API, and debug the issues very easily.

For example, the Apple Music API requires you to provision a JSON Web Key for signing your own JSON Web Tokens for making API calls. I knew understood this concept well from using Open Identity Connect in many domains at this point, but this time I had no helper
library to manage the tokens for me. I needed to sign my tokens by hand. Additionally, I was using TypeScript for the first time and returning to JavaScript after a long hiatus. AI helped me find the library I wanted (google could have done fine), show me how to use the 
library, and debugged some typos in my usage. This saved considerable time on my end and now I may not need to rely so heavily on the AI next time because it taught and I learned.

# Session Two Goals

I ended session one after I manually scraped one venue, created a CSV, and then wrote a script to lookup all of the artists to generate a playlist with 5 songs from each artist. Unfortunately, I just made a _text_ file with links to the songs.
In session two the goal will be to authenticate as my user and create the playlist.

Beyond session two, the main goal will be to widen the number of venues I can collect information from. This will require automating the scraping process. This part will include a lot of fun AI experimentation I hope!
