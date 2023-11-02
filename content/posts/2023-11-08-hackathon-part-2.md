---
title: Hackathon Part 2 - Modeling Documentation Metadata
author: Dachary Carey
layout: post
description: In which I decide how I want to structure my documentation metadata.
date: 2023-11-08 12:00:00 -0400
url: /2023/11/08/hackathon-part-2/
draft: true
image: /images/docs-writing-code-examples-hero.png
tags: [Documentation, Coding, Hackathon]
---

## Defining a data model

The nice thing about MongoDB being a document database is that you can iterate on the data model as you build out what you need. The downside is the flexibility sometimes entices less experienced developers to put a whole bunch of data in without really thinking very much about how they want to use it and what structure that data should have. I have learned some lessons through my various app projects, so I decided to sit down and figure out what structure I wanted my data to have *before* imported a bunch of it.

My Google Sheets data had these details:

- URL
- Unique pageviews
- Bounce rate
- Average session duration
- Session count
- Pages per session
- User count
- Session duration

As I wanted to be able to compare changes to the data over time, it seemed obvious I would want to track multiple weekly analytics entries for each documentation page. The only unique identifier I had in the Google Sheets data to tie the analytics back to a given page was the URL. So I would need to look up documents based on the URL in the spreadsheet. But the URL isn't a nice, human-readable string to quickly make it obvious to us what we're looking at. And I wanted to be able to track other information about a page, like what SDK it belonged to or which variation of our product it pertained to.

## Using aggregation pipelines to make the data match my desired structure 

I used aggregation pipelines to:

- Start with a [$match](https://www.mongodb.com/docs/manual/reference/operator/aggregation/match/) stage to get only a subset of the documents.
  - I used the [$regex](https://www.mongodb.com/docs/manual/reference/operator/query/regex/) operator on the `url` field of my data to get only the documents whose URLs match a specific SDK, or only the documents whose URLs match a specific page.
- Use an [$addFields](https://www.mongodb.com/docs/manual/reference/operator/aggregation/addFields/) stage to add new fields to my documents, and manually populate them. For example:
  - Add a `page` field whose value is a page title, such as "Create Objects" or "Open a Realm." I wanted to be able to track the performance of specific pages, and also provide a human-readable page title to anyone who was looking at the data.
  - Add an `sdk` field whose value is the specific SDK the page belongs to, such as "Swift SDK" or "C++ SDK." Every SDK has some of the same pages for common operations, such as "Open a Realm" and "Create Objects." But some SDKs also have SDK-specific pages, such as "Swift Concurrency" or "Actor-Isolated Realms." I wanted to be able to track changes to a specific SDK over time, so this lets me segment the data on the SDK.
  - Add a `product` field whose value is the product that the documentation pertains to. In one documentation site, we have "Device SDK" and "Realm Studio" - a GUI client to view and query the database. This lets me easily view all of the SDKs together, or view Realm Studio docs. I could have achieved something similar by using other operators in my queries. For example, instead of querying on `'product':'Device SDK'` I could have queried on documents where the SDK field did not exist, and assumed it meant that the documents pertained to Realm Studio. But that requires the person doing the queries to know a lot more about what content exists in the collection. I want my teammates to be able to add or change queries to get the information they want to answer a specific question. Being more explicit with this field makes it easier for my teammates to understand what data exists and how they can access it.
  - Add `lifecycle` and `journey` fields to segment the data based on which part of the app development lifecycle the page pertains to, or which part of the user journey is covered by the page.
  - Add a `usage` array to categorize whether the page pertains to the open source "Local" version of the database, or "Sync" database that enables devices to share data with the cloud and each other. This had to be an array, because some of our pages cover content that is the same no matter which version of the database you use. So this field could have both values.
  - Add a `page_category` field to identify whether the page is a "Task" or a "Feature" page.
- Use a [$merge](https://www.mongodb.com/docs/manual/reference/operator/aggregation/merge/) stage to output the documents to my existing collection, and match based on the `_id` field that MongoDB populated for each document when I ran the initial `mongoimport`. I used options that essentially added the new fields and values to the existing documents.
