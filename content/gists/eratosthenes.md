---
title: "Sieve of Eratosthenes"
date: 2017-12-19T12:46:20Z
draft: true
tags: ["gist", "code"]
categories: ["Gists"]
user: "arlyon"
gist: "0d90932adcb5a8a05b0bb6fa21518a04"
---

This is a really elegant and pythonic solution to the sieve of eratosthenes.
There is a solution floating around the code-scape that is seen in many
functional languages using generator expressions which (on top of choking
with python's recursion depth) doesn't properly follow the algorithm. This
is the fake sieve:

```python3
from itertools import count

def primes():
    prime_list = []
    yield 2
    gen = (x for x in count(2) if x % 2 != 0)
    
    while True:
        prime = next(gen)
        yield prime
        gen = (x for x in gen if x % prime != 0)
```

When running the above, it would appear that it works, right? 
