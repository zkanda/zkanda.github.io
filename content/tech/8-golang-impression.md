+++
author = "Zakatell Kanda"
date = 2017-05-20T21:21:11Z
description = ""
draft = false
slug = "golang-impression"
title = "golang impression"

+++

I've been very excited to use Go at work and somehow very refreshing, been meaning to learn it for some time but it's more easier for me to learn if I actually see it in action.

So far it's been very nice, though of course some stuff are not so easy. Here's one trivial matter.

* First using it with Python feel like I'm lost when context switching. Our main application is written in Python/Django and we are slowly trying to build microservices around it. They communicate via thrift.

I had to test a package that make sure it truncates to a certain unicode character limit. This is when I appreciated Python for having duck typing. I can just build a long character like this:

```python
long_unicode_char = "ひ" * 100
```

For some reason, my brain us fixated on how easy it is to do in dynamic language that it blocks from trying to search for a way in Go. But then of course, after some complaining and brain rewire you can then move forward.

So here's how you should it.

```go
s := strings.Repeat("ひ", 100)
```

Small things matters.

#### Contributing

* Tried contributing to the ecosystem, so far I'm interested on [dep](https://github.com/golang/dep), the new and hopefully will become the official package management. The people who manage it seems to be very nice. And for some interesting reason, it's one of the project that have active woman contributors, I'm sure there's a lot, but it's kind of just refreshing thought.

#### Dislike

* One inherit things that I don't like is how common it is to declare single character variable, you see this a lot in examples given in the official blog post. I see this mostly in c code but I guess I just have to get used to that.
