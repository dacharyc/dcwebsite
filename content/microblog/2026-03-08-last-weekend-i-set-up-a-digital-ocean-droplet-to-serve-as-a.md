---
title: "Last weekend, I set up a Digital Ocean droplet to serve as a self-hosted runner…"
author: Dachary Carey
date: 2026-03-08 09:58:00 -0400
url: /microblog/2026/03/08/last-weekend-i-set-up-a-digital-ocean-droplet-to-serve-as-a/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7436409892787122176
draft: false
---

Last weekend, I set up a Digital Ocean droplet to serve as a self-hosted runner for some GitHub CI I wanted to run in private repos in the new org I set up. I also set up Cloudflare Pages to host a site behind a GitHub App auth login so only people in my org can access it.

This weekend, I’ve added a FastAPI server to my Digital Ocean droplet to host a form. It’s got a Cloudflare tunnel to the site that’s behind GitHub App auth login, and I’ve slightly complicated deployment to make it more secure.

I do not have a DevOps background, but that doesn’t stop me with Claude’s assistance. BUT Claude’s DevOps guidance was not secure by default. At every step of the way, I’ve had to ask about possible attack vectors and how I can make the infra more secure. I’m sure I’ve missed things *because* I don’t have a DevOps background, even with asking about various security angles.

For my project, the exposure risk is pretty low. It’s really not the end of the world if anyone gets access to the data I’m working with. It’s all for internal/private use - I’m not working with customer data or selling a service.

But these projects have given me anxiety about all the new services folks are spinning up. I have to ask myself how many *other* people who don’t have DevOps backgrounds are now standing up infrastructure with the help of coding agents and are *not* asking the security-related questions. I admit, I’m probably going to hesitate now before signing up for someone else’s new service if it needs PII, payment info, or any data I don’t want exposed.

Also, this weekend Claude wrote a few additional details about me to its persistent memory… “prefers vi editor” (when I’m ssh-ing onto a remote server and the default editor is nano, I’m lost!) and “security-conscious.”

Ok, Claude, I think that’ll be helpful. 👍

**Links from the comments:**

- <https://www.kiva.org/lend-by-category/women>
