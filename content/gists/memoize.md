---
title: "Memoize"
date: 2017-12-19T13:07:19Z
draft: false
tags: ["gist", "code"]
categories: ["Gists"]
user: "arlyon"
gist: "81761f01165fa73b262cf5540cfc818e"
# last two are used in schema.org/SoftwareSourceCode
language: "Python"
runtime: "Python 3.6"
---

This simple decorator can significantly speed up recursive functions in python
by storing solutions in a dictionary as it runs. The code uses the new [typing
library](https://docs.python.org/3/library/typing.html) introduced in python 3.5
but it isn't strictly necessary. It supports functions with any number of input 
parameters. 

*note: that the solution dictionary is tied to the function itself so multiple calls
to the same function will reuse cached solutions from any previous calls.*