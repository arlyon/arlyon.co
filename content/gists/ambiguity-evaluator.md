---
title: "Ambiguity Evaluator"
date: 2019-03-20T17:04:49Z
draft: false
tags: ["gist", "code"]
user: "arlyon"
gist: "cd68bd679e7b8835d10a957e3b31db35"
# last two are used in schema.org/SoftwareSourceCode
language: "Python"
runtime: "Python 3.6"
---

When defining grammars, it is useful to know whether they are ambiguous,
ie. whether the same output can be attained by two separate paths through
the syntax tree. For example, given the grammar `S := aS, aaS, ε`, the string
"aa" can be obtained either by applying the 1st rule twice or the 2nd rule
once.

There is no general purpose algorithm to determine whether a grammar is
ambiguous or not, and so here is a brute-force approach which enumerates
all the words up to a certain depth (say, 5 rule applications) and
determines whether the grammar is definitely ambiguous or potentially not.
