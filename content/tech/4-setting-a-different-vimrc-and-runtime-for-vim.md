+++
author = "Zakatell Kanda"
date = 2016-11-29T01:25:20Z
description = ""
draft = false
slug = "setting-a-different-vimrc-and-runtime-for-vim"
title = "setting a different vimrc and runtime for vim"

+++

Today, I decided to update my script and search for a way to easily change how I can load `vimrc` and `.vim/` directory. And vim provides a very convenient way to do it using environment variable.

* `VIMINIT` - for switching vimrc.
* `VIMRUNTIME` - for the .vim directory.

You can use it like this:

`VIMINIT=/path/to/custom/vimrc vim`

and

`VIMRUNTIME=/path/to/.vim vim`

You can combine them both as well.

[Click here for the relevant docs](http://vimdoc.sourceforge.net/htmldoc/starting.html#$VIM)
