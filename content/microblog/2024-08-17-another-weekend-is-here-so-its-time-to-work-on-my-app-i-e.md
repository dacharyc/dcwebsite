---
title: "Another weekend is here, so it's time to work on my app - i.e"
author: Dachary Carey
date: 2024-08-17 12:07:58 -0400
url: /microblog/2024/08/17/another-weekend-is-here-so-its-time-to-work-on-my-app-i-e/
topics: [coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:ugcPost:7230606293521035265
images:
  - src: /images/microblog/2024-08-17-another-weekend-is-here-so-its-time-to-work-on-my-app-i-e-1.jpeg
    alt: "Table from a server-log report listing daily website traffic for August 1 to 17, 2024, with columns for date, number of requests, number of pages, and a red bar for each day. Traffic peaks on August 5 with 3,221 requests and 2,058 pages, with smaller peaks on August 2, 4, 6, 8, 12, and 16 of roughly 1,000 to 1,500 requests, and quiet days of 300 to 450 requests."
  - src: /images/microblog/2024-08-17-another-weekend-is-here-so-its-time-to-work-on-my-app-i-e-2.jpeg
    alt: "Table from the same server-log report breaking down requests by URL for prfocus.app. The home page received 3,291 requests and 1,182 pages, of which 2,421 requests came with a Product Hunt referral parameter, 55 and 48 from two SwiftLee advertisement variants, and 11 from upstract.com. The site stylesheet and producthunt.com appear as the next rows."
  - src: /images/microblog/2024-08-17-another-weekend-is-here-so-its-time-to-work-on-my-app-i-e-3.jpeg
    alt: "Screenshot of an Apple Numbers spreadsheet named MarketingCampaigns on its Website Activity tab. A table titled Website Activity tracks referral views per day for seven days. The ProductHunt row totals 5,418 views, with 2,058 on day one (Monday) and declining through 444 on day seven. The SwiftLee rows show 775 total views on day one, split into 16 for ad variant 1, 17 for ad variant 2, and 9 general site referrals."
draft: false
---

Another weekend is here, so it's time to work on my app - i.e. PR Focus!

My reach is very limited, so I'm doing some marketing and sponsorships to let more people know about my helpful little app. Since I'm spending money on it, I want to know how it's performing.

But - I refuse to use Google Analytics, or any tracking software that sells user data. (I also don't use Google - I pay for a private search engine that doesn't track me - and I bounce from sites where I can't opt out of cookies. But that's a story for another day!)

You know what doesn't track you or sell your data? A C library written in the 90s that aggregates data from web server logs.

I just spent 2 hours building out a spreadsheet to aggregate the data I want to track across different sources (website, Apple App Store Connect). I can pull info from my server log reports to populate the website data in my spreadsheet, and pull data from App Store Connect to track activity and sales.

Is it perfect attribution? Nope.

But it respects the privacy of my users.

Be the change you want to see.
