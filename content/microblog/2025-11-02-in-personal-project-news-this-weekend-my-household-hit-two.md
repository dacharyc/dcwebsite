---
title: "In personal project news, this weekend my household hit two key milestones in…"
author: Dachary Carey
date: 2025-11-02 09:57:05 -0500
url: /microblog/2025/11/02/in-personal-project-news-this-weekend-my-household-hit-two/
topics: [ai, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7390763890126794752
draft: false
---

In personal project news, this weekend my household hit two key milestones in our project to liberate our smart home from private clouds whose terms of data sharing are no longer acceptable to us:

- We removed the Philips Hue bridge from our house while retaining our smart lights
- We deleted our home from Apple Home while retaining all of our smart functionality

We have also disabled/removed all listening devices provided by Big Corp; no Siri, Alexa, or Google in our house.

We have lost 0 functionality.

We’re now running everything through Home Assistant. It runs in a Raspberry Pi that’s sitting with our networking equipment in our basement. It’s isolated to its own network, along with the smart hardware, so none of it ever has access to the same network as our computers and phones.

We’ve bought antennas to use our smart light bulbs and thermostats through the Zigbee and ZWave protocols without ever calling out to a server outside of our house. We’ve replaced a couple of smart plugs, two very old Hue bulbs that weren’t Zigbee compatible, and the radio module for our smart door lock with alternate hardware that’s compatible with Home Assistant.

The big question for us was how we get voice functionality. We have gotten spoiled by the ability to talk to the air and set timers or have our smart home respond to voice commands. We bought the Home Assistant Voice Preview Edition to replace Home Pod and Siri on our devices. It *does* listen, but gives us a lot more control about *when* it listens. And it is developed solely to serve the privacy-conscious Home Assistant audience. We are the consumers of a product, versus being a product whose data is being monetized in the Big Corp ecosystems.

With the new setup, we get all the same functionality (better, actually) as the old one without giving companies we don’t trust access to our data.
