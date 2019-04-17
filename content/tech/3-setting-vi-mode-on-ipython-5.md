+++
author = "Zakatell Kanda"
categories = ["technology", "ipython", "vi-mode"]
date = 2016-07-13T21:59:28Z
description = ""
draft = false
slug = "setting-vi-mode-on-ipython-5"
tags = ["technology", "ipython", "vi-mode"]
title = "setting vi mode on ipython"

+++

When I upgraded my `IPython` version to 5 and one thing that I'm missing is `vi` mode binding. When I read the changelog, they now replace `readline` with `prompt_toolkit` a pure python interactive command line prompt. This is very nice because it supports all platform that python supports.

So to bring back your vi binding, you need to do some few changes.

```sh
ipython profile create
```

This will create a default profile where you can change your IPython configuration.

Edit this file

```sh
vim ~/.ipython/profile_default/ipython_config.py
```

Uncomment this line

```sh
c.TerminalInteractiveShell.editing_mode = 'vi'
```

When starting `IPython`, you now have you `vi` binding back.
