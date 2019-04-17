+++
author = "Zakatell Kanda"
categories = ["technology", "git", "pip", "python"]
date = 2016-12-05T04:17:08Z
description = ""
draft = false
slug = "installing-python-packages-directly-from-github-using-pip"
tags = ["technology", "git", "pip", "python"]
title = "installing python packages directly from github using pip"

+++

Recently, I needed to install a package directly from Github which I'm trying to contribute into.

For example you have this url: `https://github.com/zkanda/lambda-uploader`

What you can do is just add `git+` as prefix in the URL.

Here's how you can do it!

```bash
pip install git+https://github.com/zkanda/lambda-uploader
```

But the code that I want to install is from another branch, so how do we do it? We just add @branch_name at the end of the url.

```bash
pip install git+https://github.com/zkanda/lambda-uploader/@add-variables-as-shell-parameter
```

Your package is now installed.

I'm surprised how easy it is to do it, should anyone needed it, hope this can help you.
