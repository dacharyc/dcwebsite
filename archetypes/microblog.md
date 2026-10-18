---
title: "{{ replaceRE "^\\d{4}-\\d{2}-\\d{2}-" "" .Name | replaceRE "-" " " | title }}"
author: Dachary Carey
date: {{ .Date }}
url: /microblog/{{ now.Format "2006/01/02" }}/{{ replaceRE "^\\d{4}-\\d{2}-\\d{2}-" "" .Name }}/
linkedin: # paste the "Copy link to post" URL after syndicating, then run ./import_linkedin_notes stamp <this file>
draft: true
---

