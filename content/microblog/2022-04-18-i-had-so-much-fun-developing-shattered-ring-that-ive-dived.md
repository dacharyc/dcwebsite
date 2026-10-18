---
title: "I had so much fun developing Shattered Ring that I’ve dived into developing a macOS app"
author: Dachary Carey
date: 2022-04-18 07:53:01 -0400
url: /microblog/2022/04/18/i-had-so-much-fun-developing-shattered-ring-that-ive-dived/
topics: [coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:6921787671472197632
draft: false
---

I had so much fun developing Shattered Ring that I’ve dived into developing a macOS app. This one uses Realm Sync, as I want to be able to sync the data between my work and home computers. (And I wanted a learning project for developing Sync apps!)

One problem: in development, I’m making breaking schema changes often. And unlike local Realm development, you can’t just delete the local Realm file to use the new schema: you have to terminate Sync, blow away your schema and data, and then start Sync again with your new data model. I’ve taken a more nuclear option and I just delete the Realm app, drop the MongoDB Atlas database, and create a new Realm App.

In either case, doing all this stuff manually takes way too long. So I wrote my first bash script to automate it. Hope this helps other Realm Sync developers, or even just provides some inspiration to automate away some annoying, repetitive task!

https://dev.to/dacharyc/automate-realm-app-rebuilds-3cjn
