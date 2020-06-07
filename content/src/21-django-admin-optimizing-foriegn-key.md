---
title: "Django Admin Optimizing Foriegn Key"
date: 2020-04-07T14:06:08+07:00
draft: true
---

Well oh well, I feel like I'm back to my old django days. Well, not that I'm old. But that was like 4-5 years ago. I still do it from time to time, but now, I have time again. And it's still very fun.

Anyway, to get the point.

The problem is like this.

* you have a foreign key in your models.
* that model in turn, have another foreign.
* use that in your `list_display`.

It will select your current models, join with the foreign AND the foreign of the foreign models. Talk about being very inefficient.

A quick fix:

Use `list_select_related`, this will basically limit it to just the related models.

But there's still problem with this, it will select all the fields in that object. What if you just want the `id` and `name` for example.
