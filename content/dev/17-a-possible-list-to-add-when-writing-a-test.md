+++
author = "Zakatell Kanda"
date = 2018-11-03T05:29:43Z
description = ""
draft = false
showDate = true
slug = "a-possible-list-to-add-when-writing-a-test"
title = "a possible list to think about when writing a test."

+++

When writing test, the documentation that the API you are integrating will only list all the good values that they intend to deliver to you or the other way around.

What I observed from working with my colleague Harmen is this:

- A negative test
- A positive test for the happy path
- An empty input
- A malformed data

I might add more example in the future but for now, I'll try to follow this guideline.