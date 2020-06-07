---
title: "Using Github Actions for Deploying Hugo"
date: 2020-04-06T16:09:40+07:00
showDate: true
draft: false
tags: ["dev", "github", "actions", "automation"]
---

Before, whenever I tried updating this blog, I have to follow some steps:

1. Write content
1. Commit
1. Run the publish to master scripts

Well, guess what, I can now remove that last script, because of Github actions.

But it was not without pain moving to this new platform. First, I had to read a lot of documents that don't seem to relate to what I wanted to do. Well, shame on me for reading those, but they were fun, and I learned a lot. So, although I wasted a lot of time, it doesn't matter for me, and I hope to reuse those learned lessons to my current job.

At first, I was trying to do everything by myself learning(fighting) with bash, but in the end, I used something that other people wrote.

Anyway, here's my new github actions [yaml definition](https://github.com/zkanda/zkanda.github.io/blob/189de37146d80422dfd265a3533aa94649b377bf/.github/workflows/deploy.yaml)
