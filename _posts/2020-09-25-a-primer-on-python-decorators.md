---
layout: category-post
title: "A primer on python decorators"
date:   2020-09-25
categories: python
---

## the TL;DR
A decorator gives you the ability to take existing functions or classes, add some functionality, and return them without changing the underlying code. Decorators are great for encapsulating code that is being reused often, which leads to cleaner and more pleasant to read code.

Some ways decorators are used are for timing functions, memoization (caching seen values), logging exceptions and errors, HTTP requests, and in python class methods like `@classmethod`, `@staticmethod`, and `@property`.

To write decorators, you need to know a few things about functions and how they work in Python.

## Functions
In python, functions are first-class objects, which means that:
- functions are objects (they can be referenced, passed to a variable, and returned)
- functions can be *defined* inside another function
- functions can also be passed as an argument to another function. Functions that take other functions as arguments are also called *higher-order functions*. 

Decorators can modify any **callable** object. An object is callable if it has the magic method `__call__()` implemented. These are typically functions and classes. 

Here's a simple decorator example,

    def make_pretty(func):
        def inner():
            print("I'm decorating~~")
            func()
        return inner

    def normal():
        print("normal")
    
    make_pretty(normal())

The decorator `make_pretty` takes the function it is decorating as a parameter, does something, and then calls the function parameter it was passed. However, writing it as a function composition (function within function) feels pretty clunky. We can use the equivalent syntax `@` to write,
    
    @make_pretty
    def normal():
        print("normal")

## Decorating Functions that have Arguments
We can make decorators work with functions that take an arbitrary number of parameters using `function(*args, **kwargs)` where `*args` are a tuple of positional arguments and `**kwargs` are a dictionary of keyword arguments. 

    def works_for_all(func):
        def inner(*args, **kwargs):
            print("I'm decorating something!")
            return func(*args, **kwargs)
        return inner

## Identity crisis
An unintended consequence of decorating a function is that when we return it, it takes the identity of the inner function. This can be confusing and sometimes even problematic if you need the function or class instance to retain its original properties. 

You can fix this by using builtin `wraps` decorator from `functools` or `update_wrapper` for class decorators (more about this later!) immediately after `func` is a parameter in your decorator.

    from functools import wraps

    def works_for_all(func):
        @wraps(func)
        def inner(*args, **kwargs):
            print("I'm decorating something!")
            return func(*args, **kwargs)
        return inner

### A template 
Most decorators follow the following template,

    def decorate(func):
        @wraps(func)
        def inner(*args, **kwargs):
            # do something before
            ret = func(*args, **kwargs)
            # do something after with ret
            return ret
        return inner

    @decorate
    def example():
        pass

## Applying multiple decorators
Decorators are evaluated from the bottom to the top, so if you were to decorate the following
    
    @dec2
    @dec1
    def printer(msg):
        print(msg)

Then it would evaluate it as `printer = dec2(dec1(printer))`

## Class decorators
We can also write decorators as classes. Most decorators can be written equivalently either as a function or a class, so it is case-by-case. In general, class decorators are recommended if you want to maintain the state of an object.

The `__init__()` method stores a reference to the function we're decorating and other parameters, if any. The `__call__()` method makes the class callable so that it can be called on another function. With class decorators, the `call()` method will be called instead of the decorated function and does the same thing as the `inner()` or `wrapper()` functions in previous examples.

    from functools import update_wrapper

    class emphasis:
        def __init__(self, func):
            update_wrapper(self, func) # preserve identity
            self.func = func
        
        def __call__(self, *args, **kwargs):
            ret = self.func(*args, **kwargs)
            return "<b>" + ret + "<b>"

    @emphasis
    def hello(name):
        return f"Hello{name}"


### Resources
- [Python Decorators by Redowan Delowar](https://rednafi.github.io/digressions/python/2020/05/13/python-decorators.html)