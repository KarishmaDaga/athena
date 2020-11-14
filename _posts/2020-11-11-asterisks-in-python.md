---
layout: category-post
title: "A primer on python decorators"
date:   2020-09-25
categories: python
---

# What are we not talking about?
These aren't the operators we would use to multiply or exponentiate in Python, 

    2 * 3
    >> 6

    2 ** 3
    >> 8

# What problems do these solve?
We can use `*` and `**` as prefix operators on variables to,
- pass arguments to a function
- capture arguments passed into a function
- using `*` to accept positional arguments only
- using `*` to capture items during tuple unpacking
- using `*` to unpack iterables into a list or tuple
- using `**` to unpack dictionaries into other dictionaries

## Unpacking into a function call

    fruits = ['lemon', 'apple', 'mango']
    numbers = [2, 4, 5, 0, 10]
    print(*numbers, *fruits)
    >> 2 4 5 0 10 lemon apple mango
    
