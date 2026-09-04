---
name: Python::Easy
---
# Is Python strongly typed?

Yes

# Is Python dynamically typed?

Yes

# Is Python compiled?

Yes, partially - the reference implementation (CPython) compiles source to an intermediate byte code (.pyc), but not 
to native machine code like C/Go

# Is Python an interpreted language?

Yes, but not purely interpreted line-by-line from source. The python byte code is interpreted by a virtual machine, not 
natively run by the CPU

# What is a lambda function in Python? Describe its syntax and a common use-case.

A small function defined without a name. Like a normal function it can have many parameters but only one expression. 

```python
lambda parameters: expression
```

Multiple parameters are comma separated

It is commonly used as an input higher-order functions

# What is pass in Python?

The pass statement is a null operation that does absolutely nothing when executed. It is used strictly as a syntactic 
placeholder

# In Python, why can't you leave an if statement or function completely empty, and how do you fix it?

Python uses indentation to define code blocks and you cannot leave a block completely empty; doing so throws an IndentationError. The pass statement satisfies Python's syntax requirements without running any code

# What is `*args`, `**kwargs` in function definition?

- `*args` — collects any **positional** arguments *not* already bound to a named parameter, into a `tuple`
- `**kwargs` — collects any **keyword** arguments *not* already bound to a named parameter, into a `dict`

Example:
```python
def f(a, b, *args, **kwargs):
    print(a, b)     # 1 2      <- named params claim these first
    print(args)     # (3, 4)   <- leftover positional
    print(kwargs)   # {'x': 10}  <- leftover keyword

f(1, 2, 3, 4, x=10)
```

Names `args`/`kwargs` are convention only — `*` and `**` are what matter, not the identifiers


# What is docstring in Python? How to write them? Are they required?


A docstring (documentation string) is a string literal that occurs as the first statement in a module, function, class, or method definition. Such a docstring becomes the `__doc__` special attribute of that object

Declared using triple quotes (' ' ' or " " ")
Docstrings can be accessed at runtime using `__doc__` or `help()`
