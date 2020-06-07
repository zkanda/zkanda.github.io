+++
author = "Zakatell Kanda"
date = 2017-05-27T05:20:35Z
description = ""
draft = false
slug = "gophercon-sg"
showDate = true
tags = ["golang", "conference", "singapore"]
title = "gophercon sg 2017 reflection"

+++

This is the first time attending a conference outside my country. It was pretty exciting and scary because I'm going alone but nonetheless it was an amazing experience.

I met a couple of interesting people and mostly I think it boosts more my confidence in traveling.

The **first day** was all about learning Go for Intermediate developers by [William Kennedy.](https://www.goinggo.net/)

![](/content/images/2017/05/GopherConSG---First-Day-Training.jpg)

I learned quite a lot, although I've been using Go in work for some time now, I admit that I still have a long way to go. The core that I have to learn more is how to effectively use `interface`. I was awed when he showed the tooling on profiling a Go [code](https://github.com/ardanlabs/gotraining/tree/master/topics/go/profiling/memcpu), I'm looking forward on his blog post diving deeper on these subject.

On the **second day** is where I got to see a lot of speakers, talked to some interesting people, and of course, I met one of the creators of Go [Robert Griesemer](https://research.google.com/pubs/author96.html).

![](/content/images/2017/05/exporting-go-robert-keynote.jpg)

> If the abstraction is right,
> it's worth paying its price.
> - Robert Griesemer

Personally, the talks that interest me are this:

- [Escape Analysis and Memory Profiling](https://www.youtube.com/watch?v=2557w0qsDV0&list=PLq2Nv-Sh8EbZEjZdPLaQt1qh_ohZFMDj8&index=3) - I saw this on the lecture and still very interesting to see again.
- [Distributed Scheduler Hell](https://www.youtube.com/watch?v=ttmzQbaYjjk&list=PLq2Nv-Sh8EbZEjZdPLaQt1qh_ohZFMDj8&index=6) - talk about the caveats of using new deployment platforms like mesos and kubernetes, and what are the things they are still not good at.
- [TDD for those who don't need it](https://www.youtube.com/watch?v=a6oP24CSdUg&list=PLq2Nv-Sh8EbZEjZdPLaQt1qh_ohZFMDj8&index=10) -  A very nice talk, at first I would almost think this the speaker hates TDD, but he presented in a way that a lot of people can relate. Think of the test as a set of requirements, one small task at a time and getting things done.
- [Native Go Development on GCP with the Context Package](https://www.youtube.com/watch?v=YmARDfv6VN8&index=7&list=PLq2Nv-Sh8EbZEjZdPLaQt1qh_ohZFMDj8) - this one was a short talk but gave a very brief overview of why `context` package is needed.
- [Distributed Tracing in Grab](https://www.youtube.com/watch?v=nm2A80PfYww&list=PLq2Nv-Sh8EbZEjZdPLaQt1qh_ohZFMDj8&index=8) - Talk about opentracing and how useful it is when you have microservices, this is tied somehow with `context`.

After the talk, there was an after party which is near the conference area. Lot's of food and drinks, here I was able to talk with Robert, I'm surprised by how humble he is. We talk quite a lot, I even asked him what prompted them to develop go. I can't exactly remember the exact word but he told me that Ken Thompson was trying to compile C++ code and he got frustrated, so Robert told him they have to fix it. That where it started, back in 2007. In the end, I asked him for some advice and he told me this book called [Compiler Construction](https://www.inf.ethz.ch/personal/wirth/CompilerConstruction/index.html). Well I guess have some new stuff to read.

And mostly thanks to the organizers of [GopherConSG](https://2017.gophercon.sg/), you guys did an absolutely amazing event.

Stay tuned for some post about these subjects.

Edit 1: Updated with links to include Youtube videos.
