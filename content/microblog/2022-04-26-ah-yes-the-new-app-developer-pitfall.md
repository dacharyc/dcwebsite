---
title: "Ah, yes, the new app developer pitfall"
author: Dachary Carey
date: 2022-04-26 08:59:21 -0400
url: /microblog/2022/04/26/ah-yes-the-new-app-developer-pitfall/
topics: [documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:6924703468695764994
draft: false
---

Ah, yes, the new app developer pitfall: I have reached peak "Everything is better as an app!"

Every quarter, I have a service crawl our docs site and generate a readability report. Unfortunately, due to limitations with the service, this report is a CSV that contains ~35k rows and ~77 columns. If I try opening this in Excel or Numbers, it majorly lags my computer and I can't do anything like sorting or bulk operations - it just freezes. So I need to do some pre-processing to make this CSV easier to work with.

I started looking at some command-line tools to work with CSVs, but the documentation was poor around the filtering options and I couldn't get the result I wanted. So new-app-developer me decides to grab a Swift CSV library and create an app to deal with this. Now I've got a macOS app that reads the CSV, ignores the columns I don't want, applies some filtering to the rows, and spits out a new CSV with just the data I want. The UI is a single button "Get report."

Eventually I'll beef it up so other teams at work can use it, but for now, I'll stick with my silly button in an app that doesn't even need to be an app - but I made it one anyway because I can and it makes my work more fun.
