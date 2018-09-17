---
title: "Infinite Sieve of Eratosthenes"
date: 2018-07-30T00:46:20Z
draft: false
tags: ["gist", "code"]
categories: ["Gists"]
user: "arlyon"
gist: "0d90932adcb5a8a05b0bb6fa21518a04"
# last two are used in schema.org/SoftwareSourceCode
language: "Python"
runtime: "Python 3.6"
---

The Sieve of Eratosthenes is a well known algorithm for finding primes.
Typically, it is performed on a bounded set of N numbers to yield all
primes less than N however with a few compromises, it is possible to
make an infinite generator.

There is a solution floating around the code-scape using generator
expressions which (on top of choking with python's recursion depth)
doesn't properly follow the algorithm. This is the fake sieve:

```python3
from itertools import count

def primes(in_gen):
    prime = next(in_gen)
    yield prime
    for q in primes(x for x in in_gen if x % prime != 0):
      yield q

primes(count(2))
```

When running the above, it would appear sieve-like, right? Well,
the problem is that there is no marking of primes, ie for a new prime
to be yielded it must sequentially pass each of the previous predicates.
The algorithm can be visualized as layers, one for each resursive call:

Example:

    first: count(2)
    second: (x for x in count(2) if x % 2 != 0)
    third: (x for x in (x for x in count(2) if x % 2 != 0) if x % 3 != 0)
    ...etc

Clearly this is just an inefficient way to chain if statements.
Rewritten more imperatively:

```python
from itertools import count

def primes():
    primes = []
    for candidate in count(2):
        if all(candidate % x != 0 for x in primes):
            primes.append(candidate)
            yield primes[-1]
```

The benefit of the sieve is that the primes come free, and the naive
method does not allow for that meaning many additional computations.
The gist above solves that fairly succinctly and legibly by calculating
one multiple ahead at a time for each discovered prime and storing the
prime factors for each number. When finding a number with no associated
prime factors, it is guaranteed to be prime. Otherwise we "roll forward"
each prime factor of the composite to the next candidate number.

The code explains itself better than I can, so dig in!

## Benchmarks

I compared this to a naive trial division implementation as well as the
fastest "classic" non-generator implementation I could find:
[rwh_primes2_python3](https://stackoverflow.com/a/33356284/4913983).

The generator is, with the additional overhead of "rolling forward"
primes, quite a bit slower than the bounded version by about a factor
of 20 but still looks linear from my calculations. Not bad considering
the overhead required to generate infinitely and comparing the general
legibility of the code!

> Note that the number given to the `rwh_primes2_python3` function is
larger because the others look for the nth prime and
`rwh_primes2_python3` generates all primes less than n. For that reason,
we need to give it the nth prime so it will calculate up to it.

With 10,000 primes:

    python -mtimeit -s"import eratosthenes" "from itertools import islice"  "next(islice(eratosthenes.eratosthenes(), 10000, None))" && python -mtimeit -s "import eratosthenes" "eratosthenes.rwh_primes2_python3(104729)" && python -mtimeit -s "import eratosthenes" "from itertools import islice" "next(islice(eratosthenes.trial_division(), 10000, None))"

| method | result |
| ------ | ------ |
| infinite trial division | `4.65 sec per loop` |
| infinite sieve | `39.7 msec per loop` |
| bounded sieve | `1.95 msec per loop` |


Removing the trial division allows us to up the numbers a little and
compare 1,000,000 primes:

    python -mtimeit -s"import eratosthenes" "from itertools import islice"  "next(islice(eratosthenes.eratosthenes(), 1000000, None))" && python -mtimeit -s "import eratosthenes" "eratosthenes.rwh_primes2_python3(15485863)"

| method | result |
| ------ | ------ |
| infinite sieve | `7.3 sec per loop` |
| bounded sieve | `285 msec per loop` |
