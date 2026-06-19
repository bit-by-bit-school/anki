---
name: python
---

# What are Python's built-in mutable types?

The primary built-in mutable types are `list`, `dict`, `set`, and `bytearray`. Mutable objects can have their value changed after creation.

```python
lst = [1, 2, 3]
lst.append(4)        # mutates in place

d = {"a": 1}
d["b"] = 2           # mutates in place
```

Immutable types include `int`, `float`, `complex`, `bool`, `str`, `tuple`, `bytes`, and `frozenset`.

**Source:** <https://docs.python.org/3/reference/datamodel.html#objects-values-and-types>

<AnkiTags basics types mutability junior/>

# What is the difference between `is` and `==` in Python?

`==` tests **value equality** — whether two objects have the same value.
`is` tests **identity** — whether two variables point to the exact same object in memory.

```python
a = [1, 2, 3]
b = [1, 2, 3]

a == b   # True  — same value
a is b   # False — different objects

c = a
a is c   # True  — same object
```

Use `is` only when comparing to singletons like `None`, `True`, or `False`.

**Source:** <https://docs.python.org/3/reference/expressions.html#comparisons>
<https://docs.python.org/3/reference/datamodel.html#objects-values-and-types>

<AnkiTags basics operators identity junior/>

# What values are falsy in Python?

The following objects are considered `False` in a boolean context:

- `None`
- `False`
- Zero of any numeric type: `0`, `0.0`, `0j`
- Empty sequences: `""`, `()`, `[]`
- Empty mappings: `{}`
- Empty sets: `set()`
- Objects whose `__bool__()` returns `False` or `__len__()` returns `0`

```python
bool([])   # False
bool([0])  # True  — non-empty list
bool(0.0)  # False
bool(" ")  # True  — non-empty string
```

**Source:** <https://docs.python.org/3/library/stdtypes.html#truth-value-testing>

<AnkiTags basics booleans truthiness junior/>

# What is the difference between `list.append()` and `list.extend()`?

`append(x)` adds `x` as a **single element** to the end of the list — even if `x` is itself an iterable.

`extend(iterable)` iterates over its argument and adds **each item** individually to the list.

```python
lst = [1, 2]
lst.append([3, 4])   # [1, 2, [3, 4]]  — nested list

lst = [1, 2]
lst.extend([3, 4])   # [1, 2, 3, 4]   — items added flat
```

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#more-on-lists>

<AnkiTags lists methods junior/>

# What does `list.pop()` do, and what is its time complexity?

`list.pop(index=-1)` removes and returns the item at the given index. With no argument it removes and returns the **last** item. It raises `IndexError` if the list is empty or the index is out of range.

```python
fruits = ["apple", "banana", "cherry"]
fruits.pop()     # "cherry" — O(1)
fruits.pop(0)    # "apple"  — O(n), requires shifting all elements
```

Popping from the **end** is O(1). Popping from the **beginning or middle** is O(n) because all subsequent elements must be shifted.

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#more-on-lists>

<AnkiTags lists complexity methods junior/>

# Why should you use `collections.deque` instead of a `list` for a queue?

While lists can be used as queues, inserting or popping from the **front** of a list is O(n) because all remaining elements must be shifted by one.

`collections.deque` is implemented as a doubly-linked list and provides O(1) appends and pops from **both ends**.

```python
from collections import deque

queue = deque(["Eric", "John", "Michael"])
queue.append("Terry")     # enqueue — O(1)
queue.popleft()           # dequeue — O(1) → "Eric"
```

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#using-lists-as-queues>

<AnkiTags datastructures deque collections performance junior/>

# What is a list comprehension and what is its general syntax?

A list comprehension is a concise way to create a new list by applying an expression to each element of an iterable, with an optional filter condition.

**Syntax:** `[expression for item in iterable if condition]`

```python
# Equivalent to a for-loop with append
squares = [x**2 for x in range(10)]
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

evens = [x for x in range(20) if x % 2 == 0]
# [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

Dict, set, and generator expressions follow the same pattern with `{}` or `()`.

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions>

<AnkiTags comprehensions lists junior/>

# What is the `else` clause on a `for` or `while` loop?

The `else` clause on a loop executes **only if the loop completes without hitting a `break`**. It does not run if the loop was terminated by `break`, `return`, or an exception.

```python
for n in range(2, 10):
    for x in range(2, n):
        if n % x == 0:
            print(f"{n} is composite")
            break
    else:
        # only reached if inner loop never broke
        print(f"{n} is prime")
```

This is useful for search loops: the `else` signals "item not found".

**Source:** <https://docs.python.org/3/tutorial/controlflow.html#else-clauses-on-loops>

<AnkiTags control-flow loops else junior/>

# What are the differences between tuples and lists?

| Feature     | `tuple`                        | `list`                    |
| ----------- | ------------------------------ | ------------------------- |
| Mutability  | Immutable                      | Mutable                   |
| Syntax      | `(1, 2)`                       | `[1, 2]`                  |
| Hashable    | Yes (if contents are hashable) | No                        |
| Use case    | Heterogeneous, fixed data      | Homogeneous, varying data |
| Performance | Slightly faster allocation     | Slightly slower           |

Because tuples are immutable and hashable, they can be used as **dictionary keys** or **set elements**, while lists cannot.

```python
d = {(0, 0): "origin"}   # tuple as key — OK
d[[0, 0]] = "origin"     # list as key  — TypeError
```

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences>

<AnkiTags tuples lists datastructures junior/>

# How do you create an empty set in Python, and why?

Use `set()`, not `{}`. A pair of empty curly braces `{}` creates an **empty dict**, not a set.

```python
empty_dict = {}         # dict
empty_set  = set()      # set

type({})      # <class 'dict'>
type(set())   # <class 'set'>

# Non-empty sets can use literals:
s = {1, 2, 3}
```

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#sets>

<AnkiTags sets basics junior/>

# What set operations does Python support?

Python sets support the standard mathematical set operations:

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b   # union:               {1, 2, 3, 4, 5, 6}
a & b   # intersection:        {3, 4}
a - b   # difference:          {1, 2}
a ^ b   # symmetric difference:{1, 2, 5, 6}

3 in a  # membership test:     True  — O(1) average
```

Set membership testing is O(1) average (hash-based), vs O(n) for lists.

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#sets>

<AnkiTags sets operations performance junior/>

# Are Python dictionaries ordered? When did this change?

In **Python 3.7+**, dictionaries are **guaranteed to preserve insertion order** as part of the language specification. Iterating over a dict yields keys in the order they were added.

In CPython 3.6 this was already the behaviour as an implementation detail, but it became an official language guarantee in 3.7.

```python
d = {"z": 1, "a": 2, "m": 3}
list(d.keys())   # ["z", "a", "m"] — insertion order preserved
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#dictionaries>

<AnkiTags dict ordering junior intermediate/>

# What is the difference between `dict[key]` and `dict.get(key)`?

`d[key]` raises a **`KeyError`** if the key does not exist.

`d.get(key, default=None)` returns `None` (or a specified default) if the key is missing — **no exception**.

```python
d = {"a": 1}

d["b"]           # KeyError: 'b'
d.get("b")       # None
d.get("b", 0)    # 0  — custom default
```

Use `.get()` when the key may legitimately be absent.

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#dictionaries>

<AnkiTags dict methods junior/>

# How do you iterate over a dictionary's keys, values, and key-value pairs?

```python
d = {"name": "Alice", "age": 30}

for k in d:              # iterates over keys (default)
    print(k)

for v in d.values():     # iterates over values
    print(v)

for k, v in d.items():   # iterates over (key, value) pairs
    print(k, v)
```

`d.keys()`, `d.values()`, and `d.items()` all return **view objects** — they reflect changes to the dictionary in real time.

**Source:** <https://docs.python.org/3/tutorial/datastructures.html#looping-techniques>

<AnkiTags dict iteration junior/>

# What is the LEGB rule for Python name resolution?

Python resolves names in this order: **Local → Enclosing → Global → Built-in**.

- **Local:** Variables defined inside the current function.
- **Enclosing:** Variables in any enclosing function scopes (for nested functions/closures).
- **Global:** Module-level names.
- **Built-in:** Python's built-in namespace (`len`, `print`, etc.).

```python
x = "global"

def outer():
    x = "enclosing"
    def inner():
        # x = "local"   # if this existed, it would shadow enclosing
        print(x)         # → "enclosing"
    inner()
```

**Source:** <https://docs.python.org/3/reference/executionmodel.html#resolution-of-names>

<AnkiTags scoping LEGB closures intermediate/>

# What does the `global` keyword do?

`global` declares that a name inside a function refers to the **module-level (global) variable**, allowing assignment to it without creating a new local variable.

```python
counter = 0

def increment():
    global counter
    counter += 1    # modifies module-level counter

increment()
print(counter)   # 1
```

Without `global`, `counter += 1` would raise `UnboundLocalError` because Python would treat `counter` as a local variable that hasn't been assigned yet.

**Source:** <https://docs.python.org/3/reference/simple_stmts.html#the-global-statement>

<AnkiTags scoping global intermediate/>

# What does the `nonlocal` keyword do?

`nonlocal` allows a nested function to **read and rebind a name from an enclosing (non-global) scope**. Without it, assignment creates a new local variable in the inner function, shadowing the enclosing one.

```python
def make_counter():
    count = 0
    def increment():
        nonlocal count
        count += 1
        return count
    return increment

c = make_counter()
c()   # 1
c()   # 2
```

**Source:** <https://docs.python.org/3/reference/simple_stmts.html#the-nonlocal-statement>

<AnkiTags scoping nonlocal closures intermediate/>

# What is the mutable default argument trap in Python?

Default argument values are **evaluated once** when the function is defined, not each time the function is called. If a mutable object (like a list or dict) is used as a default, it is **shared across all calls**.

```python
# BUG: the same list is reused
def append_to(val, lst=[]):
    lst.append(val)
    return lst

append_to(1)   # [1]
append_to(2)   # [1, 2]  ← not [2]!

# FIX: use None as sentinel
def append_to(val, lst=None):
    if lst is None:
        lst = []
    lst.append(val)
    return lst
```

**Source:** <https://docs.python.org/3/tutorial/controlflow.html#default-argument-values>

<AnkiTags functions pitfalls defaults intermediate/>

# What is the difference between `*args` and `**kwargs`?

`*args` collects extra **positional** arguments into a tuple.
`**kwargs` collects extra **keyword** arguments into a dictionary.

```python
def show(*args, **kwargs):
    print(args)    # tuple of positional extras
    print(kwargs)  # dict of keyword extras

show(1, 2, x=3, y=4)
# (1, 2)
# {'x': 3, 'y': 4}
```

They can be used in any combination in a signature; conventionally named `*args` / `**kwargs`, but the `*` / `**` is what matters.

**Source:** <https://docs.python.org/3/tutorial/controlflow.html#arbitrary-argument-lists>

<AnkiTags functions args kwargs junior intermediate/>

# What are positional-only and keyword-only parameters in Python?

- Parameters **before `/`** are positional-only — they cannot be passed as keyword arguments.
- Parameters **after `*`** (or `*args`) are keyword-only — they must be passed by name.

```python
def greet(name, /, greeting, *, punctuation="!"):
    return f"{greeting}, {name}{punctuation}"

greet("Alice", "Hello", punctuation=".")  # OK
greet(name="Alice", greeting="Hi")        # TypeError — name is positional-only
greet("Alice", "Hi", "?")                 # TypeError — punctuation is keyword-only
```

`/` was introduced in Python 3.8 (PEP 570).

**Source:** <https://docs.python.org/3/tutorial/controlflow.html#special-parameters>
<https://peps.python.org/pep-0570/>

<AnkiTags functions parameters intermediate/>

# What is a lambda expression?

A `lambda` creates a small **anonymous function** in a single expression. It can take any number of arguments but can only contain one expression — not statements.

```python
square = lambda x: x ** 2
square(5)   # 25

# Common use: as a sort key
pairs = [(1, "b"), (2, "a"), (3, "c")]
pairs.sort(key=lambda p: p[1])
# [(2, "a"), (1, "b"), (3, "c")]
```

For anything more complex, prefer a named `def` function for readability.

**Source:** <https://docs.python.org/3/tutorial/controlflow.html#lambda-expressions>

<AnkiTags functions lambda junior/>

# What is a closure in Python?

A closure is a function object that **remembers variables from its enclosing scope** even after that scope has finished executing. The remembered variables are called _free variables_.

```python
def multiplier(factor):
    def multiply(n):
        return n * factor    # factor is a free variable
    return multiply

double = multiplier(2)
double(5)    # 10
double(7)    # 14
```

`double.__closure__[0].cell_contents` would return `2`.

**Source:** <https://docs.python.org/3/reference/executionmodel.html#resolution-of-names>

<AnkiTags functions closures intermediate/>

# What is the late-binding closure pitfall?

In Python, closure variables are looked up **at call time**, not at definition time. This means all closures in a loop share the same binding of the loop variable — its final value.

```python
# BUG
funcs = [lambda: i for i in range(3)]
funcs[0]()   # 2  (not 0!)
funcs[1]()   # 2
funcs[2]()   # 2

# FIX: capture i's current value as a default argument
funcs = [lambda i=i: i for i in range(3)]
funcs[0]()   # 0
funcs[1]()   # 1
```

**Source:** <https://docs.python.org/3/faq/programming.html#why-do-lambdas-defined-in-a-loop-with-different-values-all-return-the-same-result>

<AnkiTags closures pitfalls lambda intermediate/>

# What is a decorator in Python?

A decorator is a callable that **wraps another function**, adding behaviour before or after it without modifying its source. Applied with `@` syntax.

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Done {func.__name__}")
        return result
    return wrapper

@log_call
def add(a, b):
    return a + b

add(1, 2)
# Calling add
# Done add
# 3
```

`@log_call` is syntactic sugar for `add = log_call(add)`.

**Source:** <https://docs.python.org/3/glossary.html#term-decorator>

<AnkiTags decorators functions intermediate/>

# Why should you use `functools.wraps` when writing decorators?

Without `functools.wraps`, the wrapper function replaces the original's `__name__`, `__doc__`, and other metadata, breaking introspection and tooling.

```python
import functools

def log_call(func):
    @functools.wraps(func)    # preserves func's metadata
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log_call
def add(a, b):
    """Adds two numbers."""
    return a + b

add.__name__   # "add"   (not "wrapper")
add.__doc__    # "Adds two numbers."
```

**Source:** <https://docs.python.org/3/library/functools.html#functools.wraps>

<AnkiTags decorators functools intermediate/>

# What does `functools.lru_cache` do?

`@functools.lru_cache(maxsize=128)` memoizes a function's return values, caching results by their arguments. "LRU" means **Least Recently Used** — the oldest entries are evicted when the cache is full.

```python
from functools import lru_cache

@lru_cache(maxsize=None)   # unbounded cache
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)

fib(100)    # computed in microseconds instead of exponential time
fib.cache_info()   # CacheInfo(hits=..., misses=..., maxsize=None, currsize=...)
```

Arguments must be **hashable** for caching to work.

**Source:** <https://docs.python.org/3/library/functools.html#functools.lru_cache>

<AnkiTags functools caching performance intermediate/>

# What is the difference between `__str__` and `__repr__`?

`__repr__` should return an **unambiguous** string representation, ideally one that could recreate the object. It is used by `repr()` and in the REPL.

`__str__` should return a **readable**, user-facing string. It is used by `print()` and `str()`.

If only `__repr__` is defined, `str()` falls back to it. The reverse is not true.

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y
    def __repr__(self):
        return f"Point({self.x!r}, {self.y!r})"
    def __str__(self):
        return f"({self.x}, {self.y})"

p = Point(1, 2)
repr(p)   # "Point(1, 2)"
str(p)    # "(1, 2)"
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#object.__repr>\_\_

<AnkiTags OOP dunder methods intermediate/>

# What is `__slots__` and when should you use it?

`__slots__` is a class-level declaration that replaces the per-instance `__dict__` with a fixed set of slot descriptors. This reduces **memory usage** (no per-instance dict overhead) and can slightly improve attribute access speed.

```python
class Point:
    __slots__ = ("x", "y")    # only x and y are allowed

    def __init__(self, x, y):
        self.x, self.y = x, y

p = Point(1, 2)
p.z = 3   # AttributeError — no __dict__, no new attributes
```

Use `__slots__` when you will create **millions of instances** and memory is a concern.

**Source:** <https://docs.python.org/3/reference/datamodel.html#slots>

<AnkiTags OOP memory slots intermediate advanced/>

# What is the Method Resolution Order (MRO)?

MRO is the order in which Python searches base classes when looking up a method or attribute in a class hierarchy. Python uses the **C3 linearization algorithm** to compute it.

```python
class A: pass
class B(A): pass
class C(A): pass
class D(B, C): pass

D.__mro__
# (<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>)
```

`super()` follows the MRO, so cooperative multiple inheritance works correctly. `super()` does not mean "parent class" — it means "next in MRO".

**Source:** <https://docs.python.org/3/howto/mro.html>

<AnkiTags OOP MRO inheritance intermediate advanced/>

# What is the difference between `@classmethod` and `@staticmethod`?

`@classmethod` receives the **class** as its first argument (`cls`). It can access or modify class state and is often used as an alternative constructor.

`@staticmethod` receives **no implicit first argument**. It is just a regular function that lives in the class namespace — no access to class or instance state.

```python
class Date:
    def __init__(self, y, m, d):
        self.year, self.month, self.day = y, m, d

    @classmethod
    def from_string(cls, s):          # alternative constructor
        y, m, d = map(int, s.split("-"))
        return cls(y, m, d)

    @staticmethod
    def is_valid_year(year):          # utility — no cls or self needed
        return 1 <= year <= 9999

Date.from_string("2024-01-15")
Date.is_valid_year(2024)    # True
```

**Source:** <https://docs.python.org/3/library/functions.html#classmethod>

<AnkiTags OOP classmethod staticmethod intermediate/>

# What does the `@property` decorator do?

`@property` turns a method into a **read-only attribute accessor**. Combined with `@name.setter` and `@name.deleter`, you can add validation and computed properties while keeping the clean attribute-access syntax.

```python
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius

    @property
    def celsius(self):
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Temperature below absolute zero")
        self._celsius = value

    @property
    def fahrenheit(self):
        return self._celsius * 9/5 + 32

t = Temperature(25)
t.celsius    # 25
t.fahrenheit # 77.0
t.celsius = -300   # ValueError
```

**Source:** <https://docs.python.org/3/library/functions.html#property>

<AnkiTags OOP property descriptors intermediate/>

# What are dunder (magic) methods? Give five common examples

Dunder methods (double-underscore, e.g. `__init__`) are special methods that Python calls **implicitly** in response to certain syntax or built-in functions. They are the hooks of Python's data model.

| Method                   | Triggered by                             |
| ------------------------ | ---------------------------------------- |
| `__init__`               | `ClassName(...)` — object initialisation |
| `__repr__`               | `repr(obj)`, REPL output                 |
| `__len__`                | `len(obj)`                               |
| `__getitem__`            | `obj[key]`                               |
| `__enter__` / `__exit__` | `with obj:` statement                    |
| `__iter__` / `__next__`  | `for x in obj:` loop                     |
| `__eq__`                 | `obj == other`                           |
| `__hash__`               | `hash(obj)`, use in sets/dicts           |

**Source:** <https://docs.python.org/3/reference/datamodel.html#special-method-names>

<AnkiTags OOP dunder datamodel intermediate/>

# How do you implement a context manager (the `with` statement)?

Implement `__enter__` and `__exit__` on a class, or use `@contextlib.contextmanager` on a generator function.

**Class-based:**

```python
class ManagedFile:
    def __init__(self, path, mode):
        self.path, self.mode = path, mode

    def __enter__(self):
        self.file = open(self.path, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_val, tb):
        self.file.close()
        return False   # do not suppress exceptions
```

**Generator-based (simpler):**

```python
from contextlib import contextmanager

@contextmanager
def managed_file(path, mode):
    f = open(path, mode)
    try:
        yield f
    finally:
        f.close()

with managed_file("data.txt", "r") as f:
    data = f.read()
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#context-managers>
<https://docs.python.org/3/library/contextlib.html#contextlib.contextmanager>

<AnkiTags contextmanager OOP intermediate/>

# What is the difference between an iterable and an iterator?

An **iterable** is any object that implements `__iter__()`, which returns an iterator. Examples: `list`, `str`, `dict`, `range`, generators.

An **iterator** is an object that implements both `__iter__()` and `__next__()`. It maintains state and produces one value at a time. When exhausted, `__next__()` raises `StopIteration`.

```python
nums = [1, 2, 3]          # iterable
it = iter(nums)            # iterator
next(it)   # 1
next(it)   # 2
next(it)   # 3
next(it)   # StopIteration
```

Every iterator is an iterable (its `__iter__()` returns itself), but not every iterable is an iterator.

**Source:** <https://docs.python.org/3/glossary.html#term-iterable>
<https://docs.python.org/3/glossary.html#term-iterator>

<AnkiTags iterators generators intermediate/>

# What is a generator function and what does `yield` do?

A **generator function** is a function containing at least one `yield` statement. Calling it returns a **generator object** (an iterator) without executing the body. Each call to `next()` resumes execution until the next `yield`.

```python
def count_up(n):
    i = 0
    while i < n:
        yield i    # suspend here, return i
        i += 1

gen = count_up(3)
next(gen)   # 0
next(gen)   # 1
next(gen)   # 2
next(gen)   # StopIteration
```

Generators are memory-efficient — they produce values **lazily**, one at a time.

**Source:** <https://docs.python.org/3/reference/datamodel.html#generator-functions>
<https://docs.python.org/3/howto/functional.html#generators>

<AnkiTags generators yield intermediate/>

# What is a generator expression?

A generator expression is like a list comprehension but uses parentheses and returns a **lazy generator object** instead of building the whole list in memory.

```python
# List comprehension — builds full list immediately
squares_list = [x**2 for x in range(1_000_000)]   # uses ~8 MB

# Generator expression — yields values one at a time
squares_gen = (x**2 for x in range(1_000_000))    # uses ~200 bytes

sum(x**2 for x in range(1000))   # generator passed directly to sum()
```

Prefer generator expressions when you only need to iterate once and the result is consumed by a built-in like `sum()`, `max()`, or `any()`.

**Source:** <https://docs.python.org/3/reference/expressions.html#generator-expressions>

<AnkiTags generators comprehensions performance intermediate/>

# What does `yield from` do?

`yield from iterable` delegates to a sub-generator (or any iterable), yielding each value from it. It also correctly propagates `send()` values, `throw()`, and `close()` into the sub-generator.

```python
def chain(*iterables):
    for it in iterables:
        yield from it    # equivalent to: for x in it: yield x

list(chain([1, 2], [3, 4], [5]))
# [1, 2, 3, 4, 5]
```

It also enables composing async coroutines (before `async/await` was introduced).

**Source:** <https://docs.python.org/3/reference/expressions.html#yield-expressions>

<AnkiTags generators yield advanced/>

# What is the difference between `try/except/else/finally`?

- `try`: code that might raise an exception.
- `except`: runs if a matching exception is raised in `try`.
- `else`: runs if **no exception** was raised in `try`.
- `finally`: always runs, regardless of whether an exception was raised or not.

```python
def read_file(path):
    try:
        f = open(path)
    except FileNotFoundError as e:
        print(f"File not found: {e}")
    else:
        # only if open() succeeded
        data = f.read()
        f.close()
        return data
    finally:
        print("Attempted to open file")  # always runs
```

**Source:** <https://docs.python.org/3/tutorial/errors.html#handling-exceptions>

<AnkiTags exceptions error-handling junior intermediate/>

# What is the difference between `raise` and `raise ... from ...`?

`raise ExceptionB from ExceptionA` explicitly **chains exceptions**, indicating that `ExceptionB` was caused by `ExceptionA`. The `__cause__` attribute is set and the traceback shows both.

`raise ExceptionB` inside an `except` block implicitly chains (stores in `__context__`), but `raise ExceptionB from None` **suppresses** the implicit chaining.

```python
try:
    result = 1 / 0
except ZeroDivisionError as e:
    raise ValueError("Invalid calculation") from e
    # "The above exception was the direct cause of..."

# Suppress the original exception in tracebacks:
raise ValueError("Invalid") from None
```

**Source:** <https://docs.python.org/3/reference/simple_stmts.html#the-raise-statement>

<AnkiTags exceptions chaining intermediate advanced/>

# How do you define a custom exception class?

Inherit from `Exception` (or a more specific built-in exception class). Add `__init__` for custom attributes if needed.

```python
class InsufficientFundsError(Exception):
    def __init__(self, amount, balance):
        self.amount = amount
        self.balance = balance
        super().__init__(
            f"Cannot withdraw {amount}; balance is {balance}"
        )

try:
    raise InsufficientFundsError(100, 50)
except InsufficientFundsError as e:
    print(e.amount, e.balance)
```

Prefer inheriting from `Exception` over `BaseException`. Only `BaseException` subclasses like `SystemExit` and `KeyboardInterrupt` should inherit directly from `BaseException`.

**Source:** <https://docs.python.org/3/tutorial/errors.html#user-defined-exceptions>

<AnkiTags exceptions OOP intermediate/>

# What is CPython's reference counting mechanism?

CPython tracks a **reference count** for each object — the number of names and containers currently pointing to it. When the count reaches zero, the object's memory is immediately reclaimed.

```python
import sys
x = []
sys.getrefcount(x)   # typically 2: x + the getrefcount argument

y = x
sys.getrefcount(x)   # 3

del y
sys.getrefcount(x)   # 2
```

Reference counting cannot handle **circular references** (e.g., `a.ref = b; b.ref = a`). CPython's supplementary cyclic garbage collector (the `gc` module) handles those.

**Source:** <https://docs.python.org/3/reference/datamodel.html#objects-values-and-types>
<https://docs.python.org/3/library/gc.html>

<AnkiTags memory internals CPython advanced/>

# What is the GIL (Global Interpreter Lock)?

The GIL is a mutex in CPython that ensures **only one thread executes Python bytecode at a time**, even on multi-core hardware. It simplifies memory management (reference counting) but limits CPU-bound parallelism via threads.

Key consequences:

- **I/O-bound tasks:** threads still help — the GIL is released during I/O waits.
- **CPU-bound tasks:** use `multiprocessing` or `concurrent.futures.ProcessPoolExecutor` to bypass the GIL.
- **C extensions** (e.g. NumPy) can release the GIL explicitly.

The GIL is a CPython implementation detail — Jython and PyPy-STM do not have it. Python 3.13 introduced experimental free-threaded builds (PEP 703).

**Source:** <https://docs.python.org/3/glossary.html#term-global-interpreter-lock>

<AnkiTags concurrency GIL threading advanced/>

# What is integer interning in Python?

CPython caches (interns) small integers in the range **-5 to 256** as singletons. Any reference to these values points to the same object, so `is` comparisons between them unexpectedly return `True`.

```python
a = 256
b = 256
a is b   # True  — same cached object

a = 257
b = 257
a is b   # False — two distinct objects (implementation detail)
```

This also applies to short string literals that look like identifiers. **Never rely on this behaviour** — use `==` for value comparison.

**Source:** <https://docs.python.org/3/reference/datamodel.html#objects-values-and-types>

<AnkiTags internals memory pitfalls advanced/>

# What is the difference between `copy.copy()` and `copy.deepcopy()`?

`copy.copy()` creates a **shallow copy** — a new object with references to the same nested objects as the original.

`copy.deepcopy()` creates a **deep copy** — a new object where all nested objects are also recursively copied. No sharing with the original.

```python
import copy

original = [[1, 2], [3, 4]]

shallow = copy.copy(original)
shallow[0].append(99)
print(original)   # [[1, 2, 99], [3, 4]] — inner list shared!

deep = copy.deepcopy(original)
deep[0].append(99)
print(original)   # [[1, 2], [3, 4]] — unaffected
```

**Source:** <https://docs.python.org/3/library/copy.html>

<AnkiTags memory copy intermediate/>

# What is `collections.defaultdict`?

`defaultdict` is a subclass of `dict` that calls a **factory function** to provide default values for missing keys, instead of raising `KeyError`.

```python
from collections import defaultdict

word_count = defaultdict(int)    # int() returns 0
for word in "the cat sat on the mat".split():
    word_count[word] += 1
# {'the': 2, 'cat': 1, 'sat': 1, 'on': 1, 'mat': 1}

graph = defaultdict(list)
graph["a"].append("b")   # no KeyError — list() creates []
```

**Source:** <https://docs.python.org/3/library/collections.html#collections.defaultdict>

<AnkiTags collections datastructures intermediate/>

# What is `collections.Counter`?

`Counter` is a `dict` subclass designed to count **hashable objects**. Elements are stored as keys; their counts are stored as values.

```python
from collections import Counter

c = Counter("abracadabra")
# Counter({'a': 5, 'b': 2, 'r': 2, 'c': 1, 'd': 1})

c.most_common(2)   # [('a', 5), ('b', 2)]

c1 = Counter("hello")
c2 = Counter("world")
c1 + c2    # Counter({'l': 5, 'o': 2, 'h': 1, 'e': 1, 'w': 1, 'r': 1, 'd': 1})
```

**Source:** <https://docs.python.org/3/library/collections.html#collections.Counter>

<AnkiTags collections Counter intermediate/>

# What is `collections.namedtuple`?

`namedtuple` creates tuple subclasses with **named fields**, giving the readability of a class with the immutability and memory efficiency of a tuple.

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)

p.x      # 3  — attribute access
p[1]     # 4  — index access still works
p._asdict()       # {'x': 3, 'y': 4}
p._replace(x=10)  # Point(x=10, y=4)
```

For richer features (mutability, default values, type hints), prefer `dataclasses.dataclass`.

**Source:** <https://docs.python.org/3/library/collections.html#collections.namedtuple>

<AnkiTags collections namedtuple datastructures intermediate/>

# What is `itertools.chain`?

`itertools.chain(*iterables)` returns an iterator that yields elements from the first iterable, then the second, and so on — concatenating iterables lazily without building a new list.

```python
from itertools import chain

result = list(chain([1, 2], [3, 4], [5, 6]))
# [1, 2, 3, 4, 5, 6]

# chain.from_iterable flattens one level:
nested = [[1, 2], [3, 4], [5]]
list(chain.from_iterable(nested))   # [1, 2, 3, 4, 5]
```

**Source:** <https://docs.python.org/3/library/itertools.html#itertools.chain>

<AnkiTags itertools iterators intermediate/>

# What does `itertools.groupby` do?

`itertools.groupby(iterable, key=None)` groups **consecutive** elements with the same key. The data **must be sorted** by the key first, otherwise non-adjacent equal elements will appear as separate groups.

```python
from itertools import groupby

data = sorted(["apple", "ant", "bat", "bear", "cat"], key=lambda x: x[0])
for letter, group in groupby(data, key=lambda x: x[0]):
    print(letter, list(group))
# a ['ant', 'apple']
# b ['bat', 'bear']
# c ['cat']
```

**Source:** <https://docs.python.org/3/library/itertools.html#itertools.groupby>

<AnkiTags itertools groupby intermediate/>

# What does `functools.partial` do?

`functools.partial(func, *args, **kwargs)` returns a new callable with some arguments of `func` **pre-filled**. Useful for adapting function signatures without writing a lambda.

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube   = partial(power, exponent=3)

square(5)   # 25
cube(3)     # 27
```

**Source:** <https://docs.python.org/3/library/functools.html#functools.partial>

<AnkiTags functools partial functional intermediate/>

# What are Python dataclasses?

`@dataclasses.dataclass` (Python 3.7+) automatically generates `__init__`, `__repr__`, and `__eq__` methods from class-level **annotated fields**, reducing boilerplate.

```python
from dataclasses import dataclass, field

@dataclass
class Inventory:
    name: str
    quantity: int = 0
    tags: list[str] = field(default_factory=list)

item = Inventory("widget", 10)
repr(item)  # "Inventory(name='widget', quantity=10, tags=[])"
```

Options include `frozen=True` (immutable, hashable), `order=True` (comparison methods), and `kw_only=True`.

**Source:** <https://docs.python.org/3/library/dataclasses.html>

<AnkiTags dataclasses OOP typing intermediate/>

# What are type hints and how do you annotate a function?

Type hints (PEP 484) are optional annotations that describe the expected types of variables, parameters, and return values. They are not enforced at runtime but aid static analysis tools like `mypy`.

```python
def greet(name: str, times: int = 1) -> str:
    return (f"Hello, {name}! " * times).strip()

# Variable annotation:
count: int = 0

# Complex types (Python 3.9+ built-in generics):
def first(items: list[int]) -> int | None:
    return items[0] if items else None
```

**Source:** <https://docs.python.org/3/library/typing.html>
<https://peps.python.org/pep-0484/>

<AnkiTags typing annotations intermediate/>

# What is `typing.Optional` and when should you use it?

`Optional[X]` is equivalent to `X | None` — the value can be of type `X` or `None`. Use it to signal that `None` is a valid value for a parameter or return.

```python
from typing import Optional

def find_user(user_id: int) -> Optional[str]:
    users = {1: "Alice", 2: "Bob"}
    return users.get(user_id)    # returns str or None

# Python 3.10+ preferred syntax:
def find_user(user_id: int) -> str | None:
    ...
```

**Source:** <https://docs.python.org/3/library/typing.html#typing.Optional>
<https://peps.python.org/pep-0484/>

<AnkiTags typing Optional intermediate/>

# What is `typing.Protocol`?

`Protocol` (Python 3.8+, PEP 544) enables **structural subtyping** ("duck typing" with type safety). A class satisfies a Protocol if it has the required methods and attributes — no explicit inheritance needed.

```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None: ...

class Circle:
    def draw(self) -> None:
        print("Drawing circle")

class Square:
    def draw(self) -> None:
        print("Drawing square")

def render(shape: Drawable) -> None:
    shape.draw()

render(Circle())   # OK — Circle has draw()
render(Square())   # OK — Square has draw()
```

**Source:** <https://docs.python.org/3/library/typing.html#typing.Protocol>
<https://peps.python.org/pep-0544/>

<AnkiTags typing Protocol duck-typing advanced/>

# What does the walrus operator `:=` do?

The walrus operator (`:=`, PEP 572, Python 3.8+) is the **assignment expression** — it assigns a value to a variable as part of a larger expression. Useful for avoiding redundant computation.

```python
# Without walrus: read() called twice if truthy
while data := file.read(1024):
    process(data)

# In a list comprehension:
results = [y for x in data if (y := transform(x)) is not None]
```

The name comes from the resemblance to walrus eyes (`:=`).

**Source:** <https://docs.python.org/3/reference/expressions.html#assignment-expressions>
<https://peps.python.org/pep-0572/>

<AnkiTags syntax walrus operators intermediate/>

# What is `match` / structural pattern matching in Python?

The `match` statement (Python 3.10+, PEP 634) compares a subject against a series of **patterns** and executes the first matching block. It goes beyond `switch`/`case` — it can destructure sequences, mappings, and class instances.

```python
def handle(command):
    match command.split():
        case ["quit"]:
            return "Quitting"
        case ["go", direction]:
            return f"Going {direction}"
        case ["pick", "up", item]:
            return f"Picking up {item}"
        case _:
            return "Unknown command"

handle("go north")      # "Going north"
handle("pick up sword") # "Picking up sword"
```

**Source:** <https://docs.python.org/3/tutorial/controlflow.html#match-statements>
<https://peps.python.org/pep-0634/>

<AnkiTags syntax match pattern-matching intermediate/>

# What is the difference between `threading.Thread` and `multiprocessing.Process`?

`threading.Thread` creates OS threads that share the same memory space but are limited by the **GIL** — only one thread executes Python bytecode at a time. Best for **I/O-bound** tasks.

`multiprocessing.Process` creates separate OS processes with their own interpreter and memory space, bypassing the GIL. Best for **CPU-bound** tasks.

```python
from threading import Thread
from multiprocessing import Process

# I/O-bound: use Thread
t = Thread(target=fetch_url, args=(url,))
t.start()

# CPU-bound: use Process
p = Process(target=compute_primes, args=(1_000_000,))
p.start()
```

**Source:** <https://docs.python.org/3/library/threading.html>
<https://docs.python.org/3/library/multiprocessing.html>

<AnkiTags concurrency threading multiprocessing intermediate advanced/>

# What is `concurrent.futures` and how do you use it?

`concurrent.futures` provides a high-level interface for running callables asynchronously using thread or process pools.

```python
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

# Thread pool for I/O-bound tasks
with ThreadPoolExecutor(max_workers=5) as executor:
    futures = [executor.submit(fetch_url, url) for url in urls]
    results = [f.result() for f in futures]

# Process pool for CPU-bound tasks
with ProcessPoolExecutor() as executor:
    results = list(executor.map(compute, data))
```

`executor.map()` is like `map()` but concurrent. `executor.submit()` returns a `Future` object.

**Source:** <https://docs.python.org/3/library/concurrent.futures.html>

<AnkiTags concurrency concurrent-futures intermediate advanced/>

# What is `asyncio` and how does `async`/`await` work?

`asyncio` is Python's framework for **single-threaded concurrent I/O** using an event loop. Coroutines defined with `async def` are suspended at `await` points, allowing other coroutines to run.

```python
import asyncio
import aiohttp

async def fetch(url: str) -> str:
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as resp:
            return await resp.text()

async def main():
    urls = ["https://example.com", "https://python.org"]
    tasks = [asyncio.create_task(fetch(u)) for u in urls]
    results = await asyncio.gather(*tasks)

asyncio.run(main())
```

`asyncio` is ideal for **I/O-bound, high-concurrency** tasks (many concurrent connections) without threads.

**Source:** <https://docs.python.org/3/library/asyncio.html>
<https://docs.python.org/3/howto/asyncio-index.html>

<AnkiTags asyncio concurrency async-await intermediate advanced/>

# What is `asyncio.gather` vs `asyncio.wait`?

`asyncio.gather(*coros)` runs multiple awaitables **concurrently**, collects all results in order, and by default raises immediately on the first exception.

`asyncio.wait(tasks, return_when=...)` gives more control — you can wait for `ALL_COMPLETED`, `FIRST_COMPLETED`, or `FIRST_EXCEPTION`, and it returns sets of `done` and `pending` tasks.

```python
# gather — simple, order-preserving results
results = await asyncio.gather(coro1(), coro2(), coro3())

# wait — fine-grained control
done, pending = await asyncio.wait(
    tasks, return_when=asyncio.FIRST_COMPLETED
)
```

**Source:** <https://docs.python.org/3/library/asyncio-task.html#asyncio.gather>
<https://docs.python.org/3/library/asyncio-task.html#asyncio.wait>

<AnkiTags asyncio concurrency advanced/>

# What does `enumerate()` do?

`enumerate(iterable, start=0)` returns an iterator of `(index, value)` tuples. It avoids manual index management in for loops.

```python
fruits = ["apple", "banana", "cherry"]

for i, fruit in enumerate(fruits):
    print(i, fruit)
# 0 apple
# 1 banana
# 2 cherry

for i, fruit in enumerate(fruits, start=1):
    print(i, fruit)
# 1 apple  ...
```

**Source:** <https://docs.python.org/3/library/functions.html#enumerate>

<AnkiTags builtins enumeration junior/>

# What does `zip()` do, and what happens if iterables have different lengths?

`zip(*iterables)` returns an iterator of tuples, pairing the i-th element from each iterable. It **stops at the shortest** iterable by default.

```python
names = ["Alice", "Bob", "Charlie"]
scores = [95, 87, 92]

for name, score in zip(names, scores):
    print(name, score)

# To zip iterables of different lengths without truncation:
from itertools import zip_longest
list(zip_longest([1, 2, 3], [4, 5], fillvalue=0))
# [(1, 4), (2, 5), (3, 0)]
```

**Source:** <https://docs.python.org/3/library/functions.html#zip>

<AnkiTags builtins zip junior intermediate/>

# What do `map()` and `filter()` do?

`map(func, iterable)` applies `func` to every element and returns a lazy iterator of results.

`filter(func, iterable)` returns a lazy iterator of elements for which `func` returns a truthy value.

```python
nums = [1, 2, 3, 4, 5]

doubled = list(map(lambda x: x * 2, nums))
# [2, 4, 6, 8, 10]

evens = list(filter(lambda x: x % 2 == 0, nums))
# [2, 4]

# Modern Pythonic equivalents using comprehensions:
doubled = [x * 2 for x in nums]
evens   = [x for x in nums if x % 2 == 0]
```

**Source:** <https://docs.python.org/3/library/functions.html#map>
<https://docs.python.org/3/library/functions.html#filter>

<AnkiTags builtins functional junior intermediate/>

# What does `functools.reduce()` do?

`reduce(func, iterable, initializer=None)` applies a two-argument function cumulatively from left to right, reducing the iterable to a single value.

```python
from functools import reduce

total = reduce(lambda acc, x: acc + x, [1, 2, 3, 4, 5])
# ((((1+2)+3)+4)+5) = 15

# With initializer:
reduce(lambda acc, x: acc + x, [1, 2, 3], 100)
# 106
```

`reduce` was a built-in in Python 2 but moved to `functools` in Python 3. For simple cases, prefer `sum()`, `max()`, or `any()`.

**Source:** <https://docs.python.org/3/library/functools.html#functools.reduce>

<AnkiTags functools functional intermediate/>

# How does Python's `sorted()` differ from `list.sort()`?

`sorted(iterable, *, key=None, reverse=False)` returns a **new sorted list** from any iterable without modifying the original.

`list.sort(*, key=None, reverse=False)` sorts the list **in place** and returns `None`.

```python
original = [3, 1, 4, 1, 5]

new = sorted(original)      # [1, 1, 3, 4, 5]  — original unchanged
original.sort()             # modifies in place, returns None

# key example — sort by string length
words = ["banana", "fig", "apple"]
sorted(words, key=len)      # ['fig', 'apple', 'banana']
```

Both use **Timsort** — O(n log n) worst case, stable sort.

**Source:** <https://docs.python.org/3/library/functions.html#sorted>
<https://docs.python.org/3/howto/sorting.html>

<AnkiTags builtins sorting junior intermediate/>

# What is Python's `__init__.py` and when is it required?

`__init__.py` marks a directory as a **Python package**, allowing its modules to be imported. In Python 3.3+, implicit namespace packages allow directories without `__init__.py` to be importable, but an explicit `__init__.py` is still needed for regular packages that want to expose a package-level API.

```
mypackage/
    __init__.py      # makes mypackage a package
    module_a.py
    module_b.py
```

```python
# __init__.py can export a public API:
from .module_a import ClassA
from .module_b import func_b
```

**Source:** <https://docs.python.org/3/reference/simple_stmts.html#import>
<https://docs.python.org/3/glossary.html#term-package>

<AnkiTags modules packages junior intermediate/>

# What is the difference between `import module` and `from module import name`?

`import module` imports the whole module object and binds it to the name `module` in the current namespace. You access attributes with `module.name`.

`from module import name` imports the specific name directly into the current namespace.

```python
import os
os.path.join("a", "b")   # explicit — clear provenance

from os.path import join
join("a", "b")             # shorter, but origin less obvious

# Wildcard imports pollute the namespace and should be avoided:
from os.path import *     # discouraged
```

`import module` is generally preferred for avoiding name clashes and making code clearer.

**Source:** <https://docs.python.org/3/reference/simple_stmts.html#import>
<https://docs.python.org/3/tutorial/modules.html>

<AnkiTags modules imports junior/>

# What is `if __name__ == "__main__":`?

When Python runs a file directly, `__name__` is set to `"__main__"`. When the file is _imported_ as a module, `__name__` is set to the module's name. This guard ensures certain code only runs when the script is executed directly, not when imported.

```python
# mymodule.py
def useful_function():
    return 42

if __name__ == "__main__":
    # Only runs when: python mymodule.py
    # Does NOT run when: import mymodule
    print(useful_function())
```

**Source:** <https://docs.python.org/3/library/__main__.html>

<AnkiTags modules scripts junior/>

# How do you measure code execution time in Python?

Use the `timeit` module, which runs code many times and reports the best average time, minimising OS scheduling noise.

```python
import timeit

# Time a simple expression
timeit.timeit("'-'.join(str(n) for n in range(100))", number=10_000)

# From command line:
# python -m timeit "'-'.join(str(n) for n in range(100))"
```

For profiling where time is spent in a full program, use `cProfile`:

```bash
python -m cProfile -s cumtime my_script.py
```

**Source:** <https://docs.python.org/3/library/timeit.html>
<https://docs.python.org/3/library/profile.html>

<AnkiTags performance profiling intermediate/>

# What is the time complexity of common Python list operations?

| Operation          | Average        | Notes                |
| ------------------ | -------------- | -------------------- |
| `lst[i]`           | O(1)           | Random access        |
| `lst.append(x)`    | O(1) amortized | Dynamic array growth |
| `lst.pop()`        | O(1)           | Pop from end         |
| `lst.pop(0)`       | O(n)           | Shifts all elements  |
| `lst.insert(0, x)` | O(n)           | Shifts all elements  |
| `x in lst`         | O(n)           | Linear scan          |
| `lst.sort()`       | O(n log n)     | Timsort              |
| `len(lst)`         | O(1)           | Cached               |

For O(1) `popleft`, use `collections.deque`.

**Source:** <https://docs.python.org/3/library/stdtypes.html#typesseq-mutable>
<https://wiki.python.org/moin/TimeComplexity>

<AnkiTags performance complexity intermediate advanced/>

# What is the time complexity of Python `dict` and `set` operations?

| Operation        | Average | Worst                  |
| ---------------- | ------- | ---------------------- |
| `d[k]` (get)     | O(1)    | O(n) (hash collisions) |
| `d[k] = v` (set) | O(1)    | O(n)                   |
| `k in d`         | O(1)    | O(n)                   |
| `del d[k]`       | O(1)    | O(n)                   |
| `s.add(x)`       | O(1)    | O(n)                   |
| `x in s`         | O(1)    | O(n)                   |

Dictionaries and sets use hash tables. Worst-case O(n) is rare and occurs only with deliberate hash collisions.

**Source:** <https://docs.python.org/3/library/stdtypes.html#mapping-types-dict>
<https://wiki.python.org/moin/TimeComplexity>

<AnkiTags performance complexity intermediate advanced/>

# Why is string concatenation in a loop slow, and what is the fix?

Strings in Python are **immutable**, so each `+=` creates a brand new string object and copies all previous characters. In a loop of n iterations this is O(n²) total work.

```python
# SLOW — O(n²)
result = ""
for word in words:
    result += word + " "

# FAST — O(n) — build a list, then join once
result = " ".join(words)

# For mixed types:
parts = []
for item in data:
    parts.append(str(item))
result = "".join(parts)
```

**Source:** <https://docs.python.org/3/library/stdtypes.html#str.join>
<https://docs.python.org/3/faq/programming.html#what-is-the-most-efficient-way-to-concatenate-many-strings-together>

<AnkiTags performance strings pitfalls junior intermediate/>

# What are f-strings and why are they preferred?

F-strings (formatted string literals, PEP 498, Python 3.6+) embed expressions inside strings using `{expression}` syntax. They are evaluated at runtime and are the fastest string formatting method.

```python
name = "Alice"
score = 98.5

# f-string (preferred)
msg = f"Hello, {name}! Score: {score:.1f}"

# Older styles (avoid in new code):
"%s got %.1f" % (name, score)          # % formatting
"{} got {:.1f}".format(name, score)   # str.format()

# Expressions and conversions:
f"{name!r}"         # repr(name)
f"{score * 2}"      # 197.0
f"{name = }"        # debugging: "name = 'Alice'"  (Python 3.8+)
```

**Source:** <https://docs.python.org/3/reference/lexical_analysis.html#formatted-string-literals>
<https://peps.python.org/pep-0498/>

<AnkiTags strings formatting junior/>

# What is string interning in Python?

Python may automatically **intern** (cache) strings that look like identifiers (contain only letters, digits, underscores) so that identical strings share the same object in memory. This makes `is` comparisons between such strings unexpectedly `True` in CPython.

```python
a = "hello"
b = "hello"
a is b   # True  — interned (CPython implementation detail)

a = "hello world"   # contains a space
b = "hello world"
a is b   # False  — not interned
```

You can force interning with `sys.intern(s)`. **Never rely on automatic interning** — use `==` for string equality.

**Source:** <https://docs.python.org/3/library/sys.html#sys.intern>

<AnkiTags strings internals CPython advanced/>

# What is a `frozenset`?

A `frozenset` is an **immutable version of `set`**. Because it is immutable and hashable, it can be used as a dictionary key or as an element of another set.

```python
fs = frozenset([1, 2, 3, 2, 1])
# frozenset({1, 2, 3})

# As a dict key:
seen = {}
seen[frozenset({1, 2})] = "pair"

# Supports all set operations (returns frozenset):
fs | frozenset([4, 5])   # frozenset({1, 2, 3, 4, 5})
```

**Source:** <https://docs.python.org/3/library/stdtypes.html#frozenset>

<AnkiTags sets frozenset datastructures intermediate/>

# How does Python's `heapq` module work?

`heapq` implements a **min-heap** using a regular Python list. The smallest element is always at index 0. Operations are O(log n).

```python
import heapq

h = [5, 3, 8, 1, 4]
heapq.heapify(h)          # in-place, O(n)
heapq.heappush(h, 2)      # O(log n)
heapq.heappop(h)          # returns 1 (smallest), O(log n)

# K largest / smallest:
heapq.nlargest(3, [1,8,2,6,3])   # [8, 6, 3]
heapq.nsmallest(3, [1,8,2,6,3])  # [1, 2, 3]
```

For a **max-heap**, store negated values: `heappush(h, -val)`.

**Source:** <https://docs.python.org/3/library/heapq.html>

<AnkiTags heapq datastructures intermediate advanced/>

# What is `pathlib` and how does it compare to `os.path`?

`pathlib.Path` (Python 3.4+) provides an **object-oriented** interface for filesystem paths. It is generally cleaner and more readable than `os.path` string operations.

```python
from pathlib import Path

p = Path("/home/user/docs") / "report.txt"   # path joining with /
p.name         # "report.txt"
p.stem         # "report"
p.suffix       # ".txt"
p.parent       # Path("/home/user/docs")
p.exists()     # True/False
p.read_text()  # file contents as str
p.write_text("hello")
list(p.parent.glob("*.txt"))   # all .txt files

# vs os.path equivalent:
import os
os.path.join("/home/user/docs", "report.txt")
```

**Source:** <https://docs.python.org/3/library/pathlib.html>

<AnkiTags pathlib filesystem intermediate/>

# How do you read and write JSON in Python?

Use the `json` module. `json.dumps()` serialises to a string; `json.loads()` deserialises from a string. `json.dump()` / `json.load()` work directly with file objects.

```python
import json

data = {"name": "Alice", "scores": [95, 87, 92]}

# Serialise to string:
s = json.dumps(data, indent=2)

# Deserialise from string:
obj = json.loads(s)

# Write to file:
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

# Read from file:
with open("data.json") as f:
    obj = json.load(f)
```

Note: JSON supports `str`, `int`, `float`, `list`, `dict`, `bool`, and `null`/`None` only.

**Source:** <https://docs.python.org/3/library/json.html>

<AnkiTags json stdlib junior intermediate/>

# What are Python's `__enter__` and `__exit__` protocols?

These are the **context manager protocol**. When a `with` statement is entered, `__enter__` is called and its return value is bound to the `as` variable. When the block exits (normally or via exception), `__exit__(exc_type, exc_val, tb)` is called.

If `__exit__` returns a **truthy** value, any exception that occurred is **suppressed** (swallowed). Returning `False` or `None` lets the exception propagate.

```python
class Timer:
    import time

    def __enter__(self):
        self.start = self.time.perf_counter()
        return self

    def __exit__(self, *args):
        self.elapsed = self.time.perf_counter() - self.start
        return False   # do not suppress exceptions

with Timer() as t:
    expensive_operation()
print(f"Took {t.elapsed:.3f}s")
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#with-statement-context-managers>

<AnkiTags contextmanager OOP datamodel intermediate/>

# What is `abc.ABC` and how do you create an abstract class?

`abc.ABC` (Abstract Base Class) is used to define interfaces. Methods decorated with `@abstractmethod` must be overridden in concrete subclasses — instantiating an unimplemented subclass raises `TypeError`.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self) -> float: ...

    @abstractmethod
    def perimeter(self) -> float: ...

class Circle(Shape):
    def __init__(self, r):
        self.r = r
    def area(self):
        import math
        return math.pi * self.r ** 2
    def perimeter(self):
        import math
        return 2 * math.pi * self.r

Shape()    # TypeError — can't instantiate abstract class
Circle(5)  # OK
```

**Source:** <https://docs.python.org/3/library/abc.html>

<AnkiTags OOP ABC abstract intermediate advanced/>

# What is Python's `unittest` framework? How do you structure a test?

`unittest` is Python's built-in testing framework, inspired by JUnit. Tests are methods on subclasses of `unittest.TestCase` whose names start with `test_`.

```python
import unittest

def add(a, b):
    return a + b

class TestAdd(unittest.TestCase):
    def setUp(self):
        # runs before each test method
        self.x = 5

    def test_positive(self):
        self.assertEqual(add(2, 3), 5)

    def test_negative(self):
        self.assertLess(add(-1, -1), 0)

    def test_raises(self):
        with self.assertRaises(TypeError):
            add("a", 1)

if __name__ == "__main__":
    unittest.main()
```

Run with: `python -m unittest discover`

**Source:** <https://docs.python.org/3/library/unittest.html>

<AnkiTags testing unittest intermediate/>

# What is `unittest.mock.patch` and when do you use it?

`unittest.mock.patch` temporarily replaces an object (function, class, or attribute) in a module with a `MagicMock` during a test, then restores the original afterwards. Use it to isolate the unit under test from dependencies like databases, APIs, or the filesystem.

```python
from unittest.mock import patch

def get_temperature():
    import requests
    r = requests.get("https://api.weather.com/temp")
    return r.json()["temp"]

# Replace requests.get during the test:
with patch("requests.get") as mock_get:
    mock_get.return_value.json.return_value = {"temp": 25}
    result = get_temperature()
    assert result == 25
    mock_get.assert_called_once()
```

**Source:** <https://docs.python.org/3/library/unittest.mock.html>

<AnkiTags testing mock unittest intermediate advanced/>

# What are common Python anti-patterns to avoid?

**1. Mutable default argument:**

```python
def f(lst=[]):   # BAD — shared across calls
def f(lst=None): # GOOD
    if lst is None: lst = []
```

**2. Comparing to `None` with `==`:**

```python
if x == None:  # BAD
if x is None:  # GOOD — None is a singleton
```

**3. Catching broad exceptions:**

```python
except Exception:  # BAD — hides bugs
except (ValueError, TypeError):  # GOOD — specific
```

**4. Using `type(x) == int` instead of `isinstance`:**

```python
type(x) == int          # BAD — doesn't handle subclasses
isinstance(x, int)      # GOOD
```

**5. Modifying a list while iterating over it:** iterate over a copy (`for item in lst[:]`) or build a new list.

**Source:** <https://docs.python.org/3/faq/programming.html>
<https://peps.python.org/pep-0008/>

<AnkiTags pitfalls best-practices intermediate/>

# What is PEP 8 and name five of its key style guidelines

PEP 8 is Python's **official style guide**, defining conventions for writing readable Python code.

Key rules:

1. **Indentation:** 4 spaces per level (no tabs).
2. **Line length:** maximum 79 characters for code, 72 for docstrings.
3. **Naming:**
   - `snake_case` for functions and variables
   - `CamelCase` for classes
   - `UPPER_CASE` for constants
4. **Blank lines:** 2 blank lines around top-level functions/classes; 1 between methods.
5. **Imports:** one per line, grouped as stdlib → third-party → local, alphabetically within groups.
6. Use `is`/`is not` with `None`; use `isinstance()` for type checks.

**Source:** <https://peps.python.org/pep-0008/>

<AnkiTags style PEP8 junior/>

# What is the difference between `__new__` and `__init__`?

`__new__(cls, ...)` is called first — it **creates and returns** the new object instance. It is a static method that receives the class as its first argument.

`__init__(self, ...)` is called after — it **initialises** the already-created instance.

```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

    def __init__(self):
        pass

a = Singleton()
b = Singleton()
a is b   # True
```

Overriding `__new__` is uncommon; mainly used for singletons, immutable types (`int`, `str`, `tuple` subclasses), and metaclasses.

**Source:** <https://docs.python.org/3/reference/datamodel.html#object.__new>\_\_

<AnkiTags OOP dunder advanced/>

# What is a metaclass in Python?

A metaclass is the **class of a class** — it controls how a class is created, just as a class controls how its instances are created. The default metaclass is `type`.

```python
class Meta(type):
    def __new__(mcs, name, bases, namespace):
        # Enforce naming convention
        for attr in namespace:
            if not attr.startswith("_") and attr != attr.lower():
                raise TypeError(f"Attribute {attr!r} must be lowercase")
        return super().__new__(mcs, name, bases, namespace)

class MyClass(metaclass=Meta):
    valid_name = 42
    # BadName = 1   # would raise TypeError
```

Metaclasses are powerful but complex — prefer class decorators or `__init_subclass__` for most use cases.

**Source:** <https://docs.python.org/3/reference/datamodel.html#metaclasses>

<AnkiTags OOP metaclass advanced/>

# What does `super()` do and how does it work in multiple inheritance?

`super()` returns a proxy object that delegates method calls to the **next class in the MRO**, not necessarily the direct parent. This enables cooperative multiple inheritance.

```python
class Animal:
    def speak(self):
        return "..."

class Dog(Animal):
    def speak(self):
        return "Woof! " + super().speak()

class Cat(Animal):
    def speak(self):
        return "Meow! " + super().speak()

class DogCat(Dog, Cat):
    pass

DogCat().speak()
# "Woof! Meow! ..."
# MRO: DogCat → Dog → Cat → Animal
```

**Source:** <https://docs.python.org/3/library/functions.html#super>
<https://docs.python.org/3/howto/mro.html>

<AnkiTags OOP super MRO inheritance intermediate advanced/>

# What are Python descriptors?

A descriptor is any object that defines `__get__`, `__set__`, or `__delete__`. When a descriptor instance is a **class attribute**, Python calls these methods instead of doing normal attribute access.

`@property`, `@classmethod`, `@staticmethod`, and many ORM fields are implemented as descriptors.

```python
class Positive:
    def __set_name__(self, owner, name):
        self.name = name

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return obj.__dict__.get(self.name)

    def __set__(self, obj, value):
        if value <= 0:
            raise ValueError(f"{self.name} must be positive")
        obj.__dict__[self.name] = value

class Product:
    price = Positive()    # descriptor instance

p = Product()
p.price = 10    # OK
p.price = -1    # ValueError
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#implementing-descriptors>
<https://docs.python.org/3/howto/descriptor.html>

<AnkiTags OOP descriptors advanced/>

# What is `__all__` in a Python module?

`__all__` is a list of strings that defines the **public API** of a module — specifically, which names are exported when a user does `from module import *`.

```python
# mymodule.py
__all__ = ["PublicClass", "public_func"]

class PublicClass:
    pass

def public_func():
    pass

def _private_func():   # not in __all__ — won't be imported by *
    pass
```

Even without `__all__`, names starting with `_` are excluded from `import *` by convention. Defining `__all__` is considered best practice for library authors.

**Source:** <https://docs.python.org/3/tutorial/modules.html#importing-from-a-package>

<AnkiTags modules API intermediate/>

# What is the difference between `bytes` and `str` in Python?

`str` is a sequence of **Unicode code points** — text data. `bytes` is a sequence of **raw 8-bit integers (0–255)** — binary data.

To convert between them you must explicitly encode/decode with a character encoding (e.g. UTF-8):

```python
text = "hello"
data = text.encode("utf-8")   # str → bytes: b'hello'
text = data.decode("utf-8")   # bytes → str: 'hello'

# Emoji — multi-byte in UTF-8:
"😊".encode("utf-8")   # b'\xf0\x9f\x98\x8a'  (4 bytes)
len("😊")              # 1 (one code point)
```

**Source:** <https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str>
<https://docs.python.org/3/howto/unicode.html>

<AnkiTags strings bytes unicode intermediate/>

# What is the `re` module? Name four common functions

The `re` module provides **regular expression** operations.

| Function               | Purpose                       |
| ---------------------- | ----------------------------- |
| `re.match(pat, s)`     | Match at the beginning of `s` |
| `re.search(pat, s)`    | Search anywhere in `s`        |
| `re.findall(pat, s)`   | Return list of all matches    |
| `re.sub(pat, repl, s)` | Replace all matches           |
| `re.compile(pat)`      | Compile pattern for reuse     |

```python
import re

email = "user@example.com"
re.search(r"[\w.]+@[\w.]+\.\w+", email).group()  # "user@example.com"

re.findall(r"\d+", "abc 123 def 456")   # ["123", "456"]

re.sub(r"\s+", "_", "hello   world")    # "hello_world"
```

**Source:** <https://docs.python.org/3/library/re.html>
<https://docs.python.org/3/howto/regex.html>

<AnkiTags stdlib re regex intermediate/>

# What is the `with` statement and when should you use it?

The `with` statement is used for **resource management** — it ensures resources like files, network connections, or locks are properly acquired and released, even if an exception occurs.

```python
# Without with: resource may leak on exception
f = open("file.txt")
try:
    data = f.read()
finally:
    f.close()

# With with: cleaner and safer
with open("file.txt") as f:
    data = f.read()
# f.close() is called automatically

# Multiple context managers:
with open("src.txt") as src, open("dst.txt", "w") as dst:
    dst.write(src.read())
```

**Source:** <https://docs.python.org/3/reference/compound_stmts.html#the-with-statement>

<AnkiTags contextmanager resources junior/>

# What is `__doc__` and how do you write docstrings?

`__doc__` is the docstring attribute of a function, class, or module. Docstrings are string literals placed at the beginning of a definition body.

```python
def add(a: int, b: int) -> int:
    """Return the sum of a and b.

    Args:
        a: First integer.
        b: Second integer.

    Returns:
        The sum of a and b.
    """
    return a + b

add.__doc__   # the full docstring
help(add)     # formatted display
```

PEP 257 defines docstring conventions. Common formats: Google style, NumPy style, reStructuredText (Sphinx).

**Source:** <https://docs.python.org/3/tutorial/controlflow.html#documentation-strings>
<https://peps.python.org/pep-0257/>

<AnkiTags docstrings documentation junior/>

# What is `__hash__` and when should you implement it?

`__hash__` returns an integer hash of an object, used by `dict` keys and `set` elements. For an object to be usable as a dict key or set element, it must be **hashable** — its hash must not change over its lifetime.

**Key rules:**

- If `__eq__` is defined without `__hash__`, Python sets `__hash__ = None` (making the object unhashable).
- Objects that compare equal must have the same hash.

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __eq__(self, other):
        return (self.x, self.y) == (other.x, other.y)

    def __hash__(self):
        return hash((self.x, self.y))   # hash a tuple of fields

p1 = Point(1, 2)
{p1: "origin"}   # OK — hashable
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#object.__hash>\_\_

<AnkiTags OOP dunder hash intermediate advanced/>

# What is Python's `struct` module used for?

`struct` packs and unpacks **C-style binary data** to/from `bytes`. Useful for binary file formats, network protocols, and interoperability with C code.

```python
import struct

# Pack: two shorts and one float (big-endian)
data = struct.pack(">2hf", 10, 20, 3.14)
# b'\x00\n\x00\x14@H\xf5\xc3'

# Unpack:
a, b, c = struct.unpack(">2hf", data)
# 10, 20, 3.140000104904175
```

Format characters: `h`=short, `i`=int, `f`=float, `d`=double, `s`=char[]. Prefix `>` = big-endian, `<` = little-endian.

**Source:** <https://docs.python.org/3/library/struct.html>

<AnkiTags stdlib binary struct advanced/>

# What is `collections.ChainMap`?

`ChainMap` groups multiple dicts (or mappings) into a single view. Lookups search each mapping in order; writes and updates only affect the **first** mapping.

```python
from collections import ChainMap

defaults = {"color": "red", "size": "large"}
overrides = {"color": "blue"}

combined = ChainMap(overrides, defaults)
combined["color"]   # "blue"   — found in overrides first
combined["size"]    # "large"  — falls back to defaults

combined["weight"] = "heavy"   # written to overrides only
```

Useful for implementing nested scopes (e.g. `argparse` layered config: CLI → env vars → config file → defaults).

**Source:** <https://docs.python.org/3/library/collections.html#collections.ChainMap>

<AnkiTags collections ChainMap intermediate advanced/>

# What does `contextlib.suppress` do?

`contextlib.suppress(*exceptions)` is a context manager that **silently swallows** the specified exception types, if they occur inside the `with` block.

```python
from contextlib import suppress
import os

# Instead of try/except:
try:
    os.remove("temp.txt")
except FileNotFoundError:
    pass

# Equivalent, more concise:
with suppress(FileNotFoundError):
    os.remove("temp.txt")
```

**Source:** <https://docs.python.org/3/library/contextlib.html#contextlib.suppress>

<AnkiTags contextlib exceptions intermediate/>

# What is `__getattr__` vs `__getattribute__`?

`__getattribute__` is called on **every attribute access** — even successful ones. Override it only with extreme care as infinite recursion is easy to trigger.

`__getattr__` is called only when the attribute is **not found** through normal means (not in instance `__dict__` or class). It is the safer hook for adding dynamic attributes.

```python
class DynamicAttrs:
    def __getattr__(self, name):
        if name.startswith("col_"):
            return f"column: {name[4:]}"
        raise AttributeError(name)

d = DynamicAttrs()
d.col_name   # "column: name"
d.missing    # AttributeError
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#object.__getattr>\_\_

<AnkiTags OOP dunder attribute-access advanced/>

# What are Python's built-in numeric types?

Python has three built-in numeric types:

- **`int`:** Arbitrary-precision integers — no overflow. Stored exactly.
- **`float`:** IEEE 754 double-precision (64-bit). Subject to floating-point rounding errors.
- **`complex`:** A pair of floats: `real` + `imag`. Literal: `3 + 4j`.

```python
# int — unlimited precision
2 ** 100   # 1267650600228229401496703205376

# float — limited precision
0.1 + 0.2 == 0.3   # False (floating-point rounding)
round(0.1 + 0.2, 1) == 0.3  # True

# For exact decimal arithmetic:
from decimal import Decimal
Decimal("0.1") + Decimal("0.2") == Decimal("0.3")  # True
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#numbers-number>
<https://docs.python.org/3/library/decimal.html>

<AnkiTags types numeric junior intermediate/>

# What is `any()` and `all()`?

`any(iterable)` returns `True` if **at least one** element is truthy. Short-circuits on the first truthy value.

`all(iterable)` returns `True` if **all** elements are truthy. Short-circuits on the first falsy value.

Both return `True` for empty iterables (vacuous truth for `all`, false for `any`).

```python
any([False, 0, "", None])      # False
any([False, 0, "", 1])         # True  (1 is truthy)
all([1, "a", [1]])             # True
all([1, 0, "a"])               # False (0 is falsy)

# Common pattern:
any(x > 5 for x in data)      # is any element > 5?
all(isinstance(x, int) for x in data)  # are all ints?
```

**Source:** <https://docs.python.org/3/library/functions.html#any>
<https://docs.python.org/3/library/functions.html#all>

<AnkiTags builtins any all junior/>

# How does Python's `del` statement work?

`del` removes a **name binding** (not necessarily the object itself). The underlying object is only destroyed when its reference count reaches zero.

```python
a = [1, 2, 3]
b = a           # b and a point to same list

del a           # removes the name 'a'; list still exists via 'b'
print(b)        # [1, 2, 3]

# del on list elements:
lst = [1, 2, 3, 4]
del lst[1:3]    # [1, 4]

# del on dict key:
d = {"x": 1, "y": 2}
del d["x"]      # {"y": 2}
```

**Source:** <https://docs.python.org/3/reference/simple_stmts.html#del>
<https://docs.python.org/3/tutorial/datastructures.html#the-del-statement>

<AnkiTags memory del intermediate/>

# What are positional and keyword arguments? How do you unpack them?

In a **function call**, you can unpack a sequence with `*` (positional) or a dict with `**` (keyword):

```python
def greet(first, last, greeting="Hello"):
    return f"{greeting}, {first} {last}!"

args = ("Alice", "Smith")
kwargs = {"greeting": "Hi"}

greet(*args)             # Hello, Alice Smith!
greet(*args, **kwargs)   # Hi, Alice Smith!

# Combine with extra args:
greet("Bob", *["Jones"])             # Hello, Bob Jones!
```

This mirrors `*args` / `**kwargs` in function _definitions_.

**Source:** <https://docs.python.org/3/tutorial/controlflow.html#unpacking-argument-lists>

<AnkiTags functions args kwargs unpacking junior intermediate/>

# What is the `typing.TypeVar` used for?

`TypeVar` is used to define **generic functions and classes** — it lets you express that two or more types in a signature must be the same, without fixing what that type is.

```python
from typing import TypeVar

T = TypeVar("T")

def first(lst: list[T]) -> T:
    return lst[0]

first([1, 2, 3])       # int
first(["a", "b"])      # str

# Bounded TypeVar — T must be int or a subtype:
N = TypeVar("N", bound=int)
def double(x: N) -> N:
    return x * 2
```

**Source:** <https://docs.python.org/3/library/typing.html#typing.TypeVar>
<https://peps.python.org/pep-0484/>

<AnkiTags typing generics TypeVar advanced/>

# What is `typing.TypedDict`?

`TypedDict` (Python 3.8+) creates a `dict` type with a **fixed set of string keys and typed values**. It is a type-checking construct — at runtime it behaves like a plain `dict`.

```python
from typing import TypedDict

class Movie(TypedDict):
    title: str
    year: int
    rating: float

m: Movie = {"title": "Inception", "year": 2010, "rating": 8.8}
# mypy/pyright will catch missing keys or wrong types
```

**Source:** <https://docs.python.org/3/library/typing.html#typing.TypedDict>
<https://peps.python.org/pep-0589/>

<AnkiTags typing TypedDict intermediate advanced/>

# What is the `__init_subclass__` hook?

`__init_subclass__` is called on a base class whenever it is **subclassed**, allowing the parent to customise or validate subclasses without a metaclass.

```python
class PluginBase:
    _registry = {}

    def __init_subclass__(cls, plugin_name=None, **kwargs):
        super().__init_subclass__(**kwargs)
        if plugin_name:
            PluginBase._registry[plugin_name] = cls

class CSVPlugin(PluginBase, plugin_name="csv"):
    pass

class JSONPlugin(PluginBase, plugin_name="json"):
    pass

PluginBase._registry
# {'csv': <class 'CSVPlugin'>, 'json': <class 'JSONPlugin'>}
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#object.__init_subclass>\_\_

<AnkiTags OOP subclassing advanced/>

# What is `__slots__` vs `__dict__` in memory usage?

By default, every Python instance stores its attributes in a `__dict__` (a hash table), which has fixed overhead (~200 bytes+). With `__slots__`, attributes are stored in a compact **C struct** — no dict, less overhead.

```python
import sys

class WithDict:
    def __init__(self, x, y):
        self.x, self.y = x, y

class WithSlots:
    __slots__ = ("x", "y")
    def __init__(self, x, y):
        self.x, self.y = x, y

a = WithDict(1, 2)
b = WithSlots(1, 2)

sys.getsizeof(a.__dict__)   # ~232 bytes
# WithSlots has no __dict__ at all
```

For large numbers of instances (e.g., a million `Point` objects), `__slots__` can cut memory use by 50%+.

**Source:** <https://docs.python.org/3/reference/datamodel.html#slots>

<AnkiTags OOP slots memory performance advanced/>

# What does `repr(obj)` fall back to if `__repr__` is not defined?

It falls back to the default implementation in `object`, which returns a string in the format `<ClassName object at 0x...>`, showing the class name and the object's memory address.

```python
class Foo:
    pass

f = Foo()
repr(f)   # "<__main__.Foo object at 0x7f3a...>"
```

This is why defining `__repr__` is a best practice — the default tells you almost nothing useful for debugging.

**Source:** <https://docs.python.org/3/reference/datamodel.html#object.__repr>\_\_

<AnkiTags OOP dunder repr junior/>

# How does Python handle integer division and floor division?

`/` always performs **true (float) division** in Python 3, even if both operands are integers.

`//` performs **floor division** — returns the largest integer ≤ the true quotient (rounds towards negative infinity).

`%` is the **modulo** operator. The relationship is: `a == (a // b) * b + (a % b)`.

```python
7 / 2      # 3.5   (float)
7 // 2     # 3     (floor)
-7 // 2    # -4    (floors towards -∞, not towards 0)

7 % 3      # 1
-7 % 3     # 2     (sign follows divisor in Python)
divmod(7, 3)   # (2, 1) — quotient and remainder
```

In Python 2, `/` was floor division for integers. Python 3's change was formalised in PEP 238.

**Source:** <https://docs.python.org/3/reference/expressions.html#binary-arithmetic-operations>
<https://peps.python.org/pep-0238/>

<AnkiTags operators arithmetic junior intermediate/>

# What is `__call__` and how do you make an object callable?

Implement `__call__` to make instances of a class behave like functions — they can be called with `()`.

```python
class Adder:
    def __init__(self, n):
        self.n = n

    def __call__(self, x):
        return x + self.n

add5 = Adder(5)
add5(10)   # 15
callable(add5)   # True
```

This is the foundation of **stateful callables** and is used internally for decorators that need to carry state.

**Source:** <https://docs.python.org/3/reference/datamodel.html#emulating-callable-objects>

<AnkiTags OOP dunder callable intermediate/>

# What is `sys.argv` and how do you use it?

`sys.argv` is a list of command-line arguments passed to a Python script. `sys.argv[0]` is the script name; `sys.argv[1:]` are the user-supplied arguments.

```python
# script.py
import sys

if len(sys.argv) < 2:
    print("Usage: script.py <name>")
    sys.exit(1)

name = sys.argv[1]
print(f"Hello, {name}!")
```

```bash
python script.py Alice
# Hello, Alice!
```

For complex argument parsing, use the `argparse` module (stdlib).

**Source:** <https://docs.python.org/3/library/sys.html#sys.argv>
<https://docs.python.org/3/library/argparse.html>

<AnkiTags stdlib sys cli junior/>

# What is `__len__` and how does it interact with `bool()`?

`__len__` is called by `len(obj)` and also by `bool(obj)` when `__bool__` is not defined. If `__len__` returns 0, `bool(obj)` is `False`; otherwise it is `True`.

```python
class Collection:
    def __init__(self, items):
        self.items = items

    def __len__(self):
        return len(self.items)

c = Collection([])
bool(c)   # False — __len__ returns 0

c = Collection([1, 2])
bool(c)   # True

len(c)    # 2
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#object.__len>\_\_

<AnkiTags OOP dunder len bool junior intermediate/>

# How do you implement comparison operators on a custom class?

Define `__eq__`, `__lt__`, `__le__`, `__gt__`, `__ge__`, `__ne__`. Alternatively, use `functools.total_ordering` — define `__eq__` and one of `__lt__`/`__le__`/`__gt__`/`__ge__`, and the rest are derived automatically.

```python
from functools import total_ordering

@total_ordering
class Version:
    def __init__(self, major, minor):
        self.major, self.minor = major, minor

    def __eq__(self, other):
        return (self.major, self.minor) == (other.major, other.minor)

    def __lt__(self, other):
        return (self.major, self.minor) < (other.major, other.minor)

v1 = Version(1, 2)
v2 = Version(2, 0)
v1 < v2    # True
v1 >= v2   # False (derived by total_ordering)
```

**Source:** <https://docs.python.org/3/library/functools.html#functools.total_ordering>

<AnkiTags OOP comparison functools intermediate/>

# What is Python's `Enum` class?

`enum.Enum` (Python 3.4+) creates **symbolic enumeration types** — named constants with unique values, iteration, membership testing, and comparison by identity.

```python
from enum import Enum, auto

class Direction(Enum):
    NORTH = auto()
    SOUTH = auto()
    EAST  = auto()
    WEST  = auto()

d = Direction.NORTH
d.name    # "NORTH"
d.value   # 1

for dir in Direction:
    print(dir)

Direction["EAST"]   # Direction.EAST  (by name)
Direction(2)        # Direction.SOUTH (by value)
```

**Source:** <https://docs.python.org/3/library/enum.html>

<AnkiTags stdlib enum intermediate/>

# What is `itertools.product`?

`itertools.product(*iterables, repeat=1)` computes the **Cartesian product** of input iterables — equivalent to nested for-loops.

```python
from itertools import product

list(product([1, 2], ["a", "b"]))
# [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]

# repeat=2: product of a sequence with itself
list(product("AB", repeat=2))
# [('A','A'),('A','B'),('B','A'),('B','B')]
```

**Source:** <https://docs.python.org/3/library/itertools.html#itertools.product>

<AnkiTags itertools combinatorics intermediate/>

# What is `itertools.combinations` vs `itertools.permutations`?

`combinations(iterable, r)` returns all **r-length combinations** (no repetition, order does not matter).

`permutations(iterable, r=None)` returns all **r-length permutations** (no repetition, order matters).

```python
from itertools import combinations, permutations

list(combinations("ABC", 2))
# [('A','B'),('A','C'),('B','C')]

list(permutations("ABC", 2))
# [('A','B'),('A','C'),('B','A'),('B','C'),('C','A'),('C','B')]
```

For combinations **with repetition**, use `combinations_with_replacement`.

**Source:** <https://docs.python.org/3/library/itertools.html#itertools.combinations>
<https://docs.python.org/3/library/itertools.html#itertools.permutations>

<AnkiTags itertools combinatorics intermediate/>

# What is `itertools.islice`?

`itertools.islice(iterable, stop)` / `islice(iterable, start, stop, step)` returns a lazy **slice** of an iterator — without materialising the whole iterable. Useful for slicing infinite generators.

```python
from itertools import islice, count

# count() is an infinite iterator: 0, 1, 2, ...
first_ten = list(islice(count(), 10))
# [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Skip first 5, take next 3:
list(islice(count(), 5, 8))   # [5, 6, 7]
```

**Source:** <https://docs.python.org/3/library/itertools.html#itertools.islice>

<AnkiTags itertools slicing intermediate/>

# What does `vars()` return?

`vars(obj)` returns the `__dict__` attribute of `obj` — the instance's namespace dictionary. Called without arguments, it returns the local namespace (like `locals()`).

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

p = Point(3, 4)
vars(p)   # {'x': 3, 'y': 4}

# Modifying the returned dict modifies the object:
vars(p)["z"] = 5
p.z   # 5
```

**Source:** <https://docs.python.org/3/library/functions.html#vars>

<AnkiTags builtins introspection intermediate/>

# What is `getattr`, `setattr`, `hasattr`, `delattr`?

These four built-ins provide **dynamic attribute manipulation** using string names — useful for metaprogramming and reflection.

```python
class Dog:
    def __init__(self):
        self.name = "Rex"

d = Dog()

getattr(d, "name")          # "Rex"
getattr(d, "age", None)     # None — default if not found

setattr(d, "age", 5)        # d.age = 5
hasattr(d, "age")           # True
hasattr(d, "breed")         # False

delattr(d, "age")           # del d.age
```

**Source:** <https://docs.python.org/3/library/functions.html#getattr>

<AnkiTags builtins introspection intermediate/>

# What is the `__missing__` method on a dict subclass?

`__missing__` is called by `dict.__getitem__` when a **key is not found**. It is the hook used internally by `defaultdict` — you can define it on your own `dict` subclass to provide custom missing-key behaviour.

```python
class AutoVivification(dict):
    """Nested dict that creates sub-dicts on missing access."""
    def __missing__(self, key):
        self[key] = AutoVivification()
        return self[key]

d = AutoVivification()
d["a"]["b"]["c"] = 42
d["a"]["b"]["c"]   # 42
```

**Source:** <https://docs.python.org/3/library/stdtypes.html#dict.__missing>\_\_

<AnkiTags dict OOP advanced/>

# What is `threading.Lock` and why is it needed?

A `Lock` is a synchronisation primitive. It prevents **race conditions** when multiple threads read and modify shared state concurrently. A lock can only be held by one thread at a time.

```python
import threading

counter = 0
lock = threading.Lock()

def increment():
    global counter
    with lock:
        counter += 1    # only one thread can be here at a time

threads = [threading.Thread(target=increment) for _ in range(1000)]
for t in threads: t.start()
for t in threads: t.join()

print(counter)   # reliably 1000 (without lock it could be less)
```

**Source:** <https://docs.python.org/3/library/threading.html#threading.Lock>

<AnkiTags threading concurrency locks intermediate advanced/>

# What is `multiprocessing.Queue` used for?

`multiprocessing.Queue` is a **process-safe** FIFO queue for passing data between processes. Unlike `queue.Queue`, it uses pipes and locks internally to work across process boundaries.

```python
from multiprocessing import Process, Queue

def worker(q, n):
    q.put(n ** 2)

if __name__ == "__main__":
    q = Queue()
    procs = [Process(target=worker, args=(q, i)) for i in range(5)]
    for p in procs: p.start()
    for p in procs: p.join()
    results = [q.get() for _ in range(5)]
    print(sorted(results))  # [0, 1, 4, 9, 16]
```

**Source:** <https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Queue>

<AnkiTags multiprocessing concurrency intermediate advanced/>

# How does Python's `logging` module differ from using `print()`?

The `logging` module provides configurable **severity levels** (DEBUG, INFO, WARNING, ERROR, CRITICAL), output formatting, handlers (file, stream, syslog), and can be enabled/disabled per module without changing code. `print()` always outputs and mixes with normal program output.

```python
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s %(levelname)s %(name)s: %(message)s"
)

logger = logging.getLogger(__name__)

logger.debug("Low-level detail")
logger.info("Application started")
logger.warning("Disk almost full")
logger.error("Failed to connect: %s", err)
```

**Source:** <https://docs.python.org/3/library/logging.html>
<https://docs.python.org/3/howto/logging.html>

<AnkiTags logging stdlib intermediate/>

# What is `__future__` and why was it used?

`from __future__ import feature` enabled **backported language features** from a newer Python version. It was widely used in Python 2 to opt into Python 3 behaviours.

```python
# Python 2 code that behaves like Python 3:
from __future__ import print_function   # print() as function
from __future__ import division         # / is true division
from __future__ import unicode_literals # string literals are unicode
from __future__ import annotations      # PEP 563 — deferred annotation evaluation
```

In modern Python 3 code the only remaining relevant import is `from __future__ import annotations` (PEP 563, deferred type annotation evaluation — though PEP 649 may supersede it).

**Source:** <https://docs.python.org/3/library/__future__.html>

<AnkiTags imports annotations intermediate/>

# What is a `NamedTuple` from `typing`?

`typing.NamedTuple` is the typed version of `collections.namedtuple`. Fields are declared with class syntax and type annotations, providing better IDE support.

```python
from typing import NamedTuple

class Employee(NamedTuple):
    name: str
    department: str
    salary: float = 50_000.0

e = Employee("Alice", "Engineering", 95_000.0)
e.name         # "Alice"
e._asdict()    # {'name': 'Alice', 'department': 'Engineering', 'salary': 95000.0}

# Immutable:
e.name = "Bob"   # AttributeError
```

**Source:** <https://docs.python.org/3/library/typing.html#typing.NamedTuple>

<AnkiTags typing namedtuple intermediate/>

# What is the purpose of `object.__new__` returning something other than an instance of `cls`?

If `__new__` returns an instance that is **not** an instance of `cls`, then `__init__` is **not called**. This is used for immutable type subclasses (where the value must be set during construction, not after).

```python
class InternedStr(str):
    """A str subclass that always interns itself."""
    import sys

    def __new__(cls, value):
        return cls.sys.intern(super().__new__(cls, value))

# str.__init__ has nothing useful to do since str is immutable,
# so this is fine.
```

Also used to return a cached instance (e.g., Singleton, Flyweight patterns).

**Source:** <https://docs.python.org/3/reference/datamodel.html#object.__new>\_\_

<AnkiTags OOP internals advanced/>

# What is `contextlib.ExitStack`?

`ExitStack` is a context manager that manages a **dynamic number of context managers**. You can push context managers at runtime, and they are exited LIFO when the `ExitStack` exits.

```python
from contextlib import ExitStack

files_to_open = ["a.txt", "b.txt", "c.txt"]

with ExitStack() as stack:
    handles = [stack.enter_context(open(f)) for f in files_to_open]
    # all files guaranteed to be closed on exit, even on exception
    data = [f.read() for f in handles]
```

Useful when you don't know the number of resources at write time.

**Source:** <https://docs.python.org/3/library/contextlib.html#contextlib.ExitStack>

<AnkiTags contextlib advanced/>

# What is `inspect` module used for?

The `inspect` module provides functions to **introspect live objects** — their source code, call signature, class hierarchy, and stack frames.

```python
import inspect

def greet(name: str, greeting: str = "Hello") -> str:
    """Return a greeting."""
    return f"{greeting}, {name}!"

inspect.signature(greet)
# <Signature (name: str, greeting: str = 'Hello') -> str>

inspect.getsource(greet)   # returns source code as a string

inspect.isfunction(greet)  # True
inspect.isclass(int)       # True

inspect.stack()            # current call stack as list of FrameInfo
```

**Source:** <https://docs.python.org/3/library/inspect.html>

<AnkiTags stdlib introspection advanced/>

# What is `__enter__` expected to return in a context manager?

`__enter__` should return the **value to bind to the `as` variable** in the `with` statement. Commonly it returns `self` (the context manager itself), a resource it manages (e.g. an open file), or `None`.

```python
class Transaction:
    def __enter__(self):
        self.start()
        return self          # bound to `as tx`

    def __exit__(self, exc_type, *_):
        if exc_type:
            self.rollback()
        else:
            self.commit()
        return False

with Transaction() as tx:
    tx.execute("INSERT ...")
```

**Source:** <https://docs.python.org/3/reference/datamodel.html#context-managers>

<AnkiTags contextmanager OOP intermediate/>
