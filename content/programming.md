---
title: Programming
permalink: /programming/
image: '/images/programming.png'
---

I'm a self-taught programmer dabbling in Swift, with the occasional Python, TS or Go as the project requires.

I used to have a whole bunch here about the various classes I'm working through and a couple of my personal projects, but time moves really quickly in the programming world and most of it isn't that relevant anymore. Instead, let me highlight some more recent things.

Shattered Ring
--------------

[Shattered Ring](https://shatteredring.com) is a SwiftUI app that uses Realm Database. It's an iOS app I wrote to track Elden Ring play through details - namely, important NPCs, Locations and Quests in the game. It's my first app that I've [released to the App Store](https://apps.apple.com/app/shattered-ring/id1613271666), and I know it's a tiny hobby app but I'm so chuffed to have it out in the world.

Bonus: it's also great for tracking D&D or other TTRPG games! I'm using it to keep track of two D&D campaigns I'm in.

SwiftUI Template App for MongoDB Realm with Sync
------------------------------------------------

Last year, my team introduced a series of simple "template apps" in each of the SDK languages to make it easier for developers to get started using MongoDB Realm with Sync. These are simple templates that are pre-populated with some of the basic code to work with Realm and Sync, that are automagically connected to a Sync backend that persists data in MongoDB Atlas. You sign up, select the "Create a Template App" option, a backend is created for you and you can use the Realm CLI to get the template app code. It's a pretty spiffy system that my very smart teammates have put together.

My contribution to it is the SwiftUI Template App, which you can see in the [Realm Template Apps GitHub repository](https://github.com/mongodb-university/realm-template-apps/tree/main/swiftui). So now, if you want to get started using SwiftUI with MongoDB Realm / Sync, you can use my template app to get started playing around!

PR Watcher
----------

PR Watcher is a GitHub tracking app I'm writing for macOS. My regular workflow has me keeping track of my own GitHub pull requests, pull requests where I'm a reviewer, and pull requests by the upstream engineering team whose work I document. I've also been tracking my team's pull requests so I can put out a weekly update about the documentation updates we've made. Kay has similar but not identical lists of PRs he watches, so we've each discussed what would be useful to us in tools to do this and have been writing our own versions.

I'm writing my tool as a macOS app with SwiftUI and Realm. It has been interesting to write a macOS app with SwiftUI, and stumble across the differences between writing for it and iOS. It's also more complicated, as the GitHub API requires me to make a whole series of network calls to get the information I want to track, so I'm having fun figuring out how to do all those network calls and turn them into Realm objects I can display in my app and persist to the DB.

Coffeelicious/Tealicious
------------------------

Coffeelicious for me, and Tealicious for the hubby, are trackers where we can keep tabs on our preferred beverages. Whenever I go to order coffee beans from various roasters, I can't remember what I've liked and haven't liked from the prior orders, and I'm also not learning more general details about flavor profiles I like, coffee characteristics I like, etc. I want an app where I can rate the coffee I drink, and have that app build a profile for me to get recommendations about coffee I'd like. I've been through a few iterations of this and have never quite gotten it to done, but after my progress with Shattered Ring, I'm hopeful these apps will follow shortly.