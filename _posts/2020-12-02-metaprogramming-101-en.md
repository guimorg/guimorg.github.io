---
title: "Metaprogramming 101"
tags: [software, python]
toc: true
lang: en
ref: metaprogramming-101
excerpt: Metaclasses and Metaprogramming in Python
header:
  image: "/images/metaprogramming/post.png"
---

Before understanding what metaprogramming is, why don't we refresh a little bit about Python classes, objects, and metaclasses?

## Quick Refresher

### Objects in Python

In Python, [as you may know](https://www.codingninjas.com/blog/2020/08/27/how-everything-in-python-is-an-object/), everything is an object. If you start exploring the `type` of your functions and variables that becomes rather obvious:

```python
variable = 25
type(variable)
# <class 'int'>

variable_string = "hello-world"
type(variable_string)
# <class 'str'>

import inspect

inspect.isclass(str)
# True

inspect.isclass(int)
# True
```

The `type` function helps us to get the `class` of a Python object. But.... if everything in Python is an object, what a `class` is?

```python
class WhatIsLove:
    pass

type(WhatIsLove)
# <class 'type'>

import inspect

inspect.isclass(WhatIsLove)
# True

inspect.isclass(type(WhatIsLove))
# True
```

So, as you saw above, a class is *also an object*, and that is pretty weird, right? In fact, when we used the `type` function to find out the class of our class (inception?) we discovered that it was the`type` class (inception, again?). Let's make things more confuse:

```python
isinstance(WhatIsLove, type)
# True

isinstance(type, type)
# True

isinstance(WhatIsLove, WhatIsLove)
# False
```

So.... A class is a an instance of `type` and the `type` class is also an instance of `type`? Wow, that is weird!

So what is this `type` function and class type? In Python docs you will find more information (and actually a lot of information) on this, but I will point you to a [related article](https://docs.python.org/3/reference/datamodel.html#metaclasses). Basically, **all classes are constructed using `type`**, meaning that all classes are derived from `type`:

```python
isinstance(int, type)
# True

isinstance(55, type)
# True
```

OK, OK. We can take a break here to let this sink in our head!

### Classes and Metaclasses

(If you need a quick-refresh on [OOP in Python](https://www.programiz.com/python-programming/object-oriented-programming))

So, we know that when we are designing a class in Python we use `__init__` to initialize the object's attributes:

```python
class Painting:
 
    def __init__(self, author, date, name, height, width, cost="unknown"):
        self.author = author
        self.date = date
        self.name = name
        self.height = height
        self.width = width
        self.cost = cost
    
    def __repr__(self):
        return f"Paiting '{self.name}' by {self.author} ({self.date}). USD {self.cost}. Dimensions {self.height} X {self.width}"
    
    def __str__(self):
        return self.__repr__
    
    def get_area(self):
        return self.height * self.width
```

Above we can see a pretty naive implementation of a Painting class, let's take a look at what happens in the backstage:

```python
Painting.__dict__
# {'__module__': '__main__', '__init__': <function Painting.__init__ at 0x10be9a3b0>, '__repr__': <function Painting.__repr__ at 0x10be9a440>, '__str__': <function Painting.__str__ at 0x10be9a4d0>, 'area': <property object at 0x10bd899b0>, '__dict__': <attribute '__dict__' of 'Painting' objects>, '__weakref__': <attribute '__weakref__' of 'Painting' objects>, '__doc__': None}

starry_night = Painting(
    author="Vincent van Gogh",
    date=1889,
    name="The Starry Night",
    height=73.7,
    width=92.1
)

starry_night.__dict__
# {'author': 'Vincent van Gogh', 'date': 1889, 'name': 'The Starry Night', 'height': 73.7, 'width': 92.1, 'cost': 'unknown'}
```

See how the Painting class holds defined methods and magic methods? Once we instantiate the class, we will have its attributes populated and resulting in a Painting object. 

But still, how this stuff really happens? Well, as we saw in the beginning, here comes the `type`:

```python
# We can use type to know the class of an object
variable = "123"
type(variable)
# <class 'str'>

# Or to create classes!
def get_area(self):
    return self.height * self.width

Painting = type("Painting", (), {"author": "Vincent van Gogh", "date": 1889, "name": "The Starry Night", "height": 73.7, "width": 92.1, "get_area": get_area})

print(Painting)
# <class '__main__.Painting'>

starry_night = Painting()
starry_night.author
# 'Vincent van Gogh'
starry_night.name
# 'The Starry Night'
starry_night.get_area()
# 6787.7699999999995

# We can also inherit from other classes
class SaoPauloState:
    def __init__(self):
        self.state = "Sao Paulo"
        self.country = "Brasil"


SaoPauloCity = type("SaoPauloCity", (SaoPauloState), {"city": "Sao Paulo"})

sao_paulo = SaoPauloCity()
sao_paulo.city
# 'Sao Paulo'

sao_paulo.state
# 'Sao Paulo'

sao_paulo.country
# 'Brazil'
```

In practice, this is what happens in the background when we intantiate a new class, dynamically (or on-the-fly) Python assembles all base classes (usually `object`) needed and also the 'body' of the class (attributes, methods, ...).

And that, folks, is what a `metaclass` does: it creates classes! Just as a class create objects (and customize them, according to code), **metaclasses create classes** (and customize them, according to code)!

#### OK, OK... How all of that works?

Basically, everytime you create and instatiate a class this happens:

- Python checks for the metaclass' class (this happens both at the class level and module level, check here for [more info](https://www.python.org/dev/peps/pep-3115/))
  - If there is no metaclass defined, `type` - the default one - is used
- Using the metaclass, Python creates a class object in-memory - remember the metaclass will create a class object, not a class instance!
- Finally, instantiates the class's object!

A very nice thing to know is that metaclasses don't actually need to be classes, you can also use functions as metaclasses!

Let's see a very quick and easy example of how that actually works:

```python
import re


def camel_case_attributes(future_class_name, future_class_parents, future_class_attributes):
    """Returns a class object with attributes turned into camelCase."""
    # Before creating our 
    camelCase_attributes = {
        re.sub(r'_([a-z])', lambda m: m.group(1).upper(), attribute): value
        for attribute, value in future_class_attributes.items()
    }
    
    # Here type creates the class object
    return type(future_class_name, future_class_parents, CamelCase_attributes)


class WhatIsLove(metaclass=camel_case_attributes):
    baby_dont_hurt_me = "no more"

WhatIsLove.baby_dont_hurt_me
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# AttributeError: type object 'WhatIsLove' has no attribute 'baby_dont_hurt_me'

WhatIsLove.babyDontHurtMe
# 'no more'

haddaway = WhatIsLove()
haddaway.babyDontHurtMe
# 'no more'
```

That's something, hm? Our function was used as a metaclass and we were able to make changes to the **class variable** before instantiating an object!

## Finally, Metaprogramming

Now we have a more general idea of metaclasses in Python and how they work. Having that in mind, when we talk about metaprogramming we are basically talking about writing a program that manipulates programs. That is different from what we normally do because we usually only manipulate data but in the case of metaprogramming we are *manipulating code* (if you know LISP, for example, you have knowledge of doing this).

I'll give two curious examples of metaprogramming so you can get the feel of it:

```shell
#!/bin/bash
# taken from https://en.wikipedia.org/wiki/Metaprogramming#Code_generation

# Creates shebang for the program
echo '#!/bin/bash' > program
# Instructions for the program to print numbers 1-100
for i in $(seq 100)
do
	echo "echo $i" >> program
done
# Program is now executable
chmod +x program
```

This is a very simple program that creates another program to print number between 1 and 100!

```python
#!/usr/bin/env python3
# This is a quine, a self-replicating code! A code that writes itself, how about that?
# Taken from here: https://www.youtube.com/watch?v=a-zEbokJAgY&list=UUSHZKyawb77ixDdsGog4iWA
s='s=%r;print(s%%s)';print(s%s)
```

This example is of a Python [*quine*](https://en.wikipedia.org/wiki/Quine_(computing)#:~:text=A%20quine%20is%20a%20computer,%22self%2Dcopying%20programs%22.), a code that when executed replicates itself, try running it on your interpreter!

### A more useful example

Well, in case you didn't know, if you ever created a [Python decorator](https://realpython.com/primer-on-python-decorators/), you've used metaprogramming (tap yourself on the back, you're awesome!). Just to refresh, a Python decorator is a function that wraps a function or a class and that it can changes its behavior. Let's see a very naive example:

```python
from functools import wraps
from urllib.request import urlopen, Request
import random

URL = "https://raw.githubusercontent.com/EugeneKay/git-jokes/lulz/Jokes.txt"
request = Request(URL)
response = urlopen(request)
jokes = response.read().decode("utf-8")
response.close()
jokes = jokes.split("\n")


def print_jokes(n_jokes=1):
    def inner(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            print("Some random jokes for you:\n")
            print("\n".join(random.choices(jokes, k=n_jokes)), sep="\n")
            return func(*args, **kwargs)
        return wrapper
    return inner


def helloworld():
    print("Hello World")

helloworld()
# Hello World

helloworld = print_jokes(n_jokes=5)(helloworld)

helloworld()
# Some random jokes for you:\n
# git off!
# What we push in life echoes in eternity
# May the forks be with you
# You can store your code in it!
# The problem with Git jokes ? well, everyone has their own version :)
# Hello World
```

Nice! This is a first attempt at metaprogramming; you see, we wrote code that was able to manipulate the code itself, just as we would with data. And we can also do that with classes, not only functions:

```python
def cls_wrapper(cls):
    for name, val in vars(cls).items():
        if callable(val):
            setattr(cls, name, print_jokes(n_jokes=2)(val))
    return cls


@cls_wrapper
class HelloWorld:
    def method1(self):
        print("method1 was called!")
    def method2(self):
        print("method2 was called!")
    def method3(self):
        print("method3 was called!")


hellow = HelloWorld()

hellow.method1()
# Some random jokes for you:\n
# git pull a day keeps the conflicts away
# I bet you thought this was a joke. Nope, git-testa.
# method1 was called!

hellow.method2()
# Some random jokes for you:\n\n
# hellow.method3()git-stash: The sock drawer of version control
# git-bisect: The good, the bad and the... uhh... skip
# method2 was called!

hellow.method3()
# Some random jokes for you:\n
# Don't forgit to bring a towel
# Be careful when rewriting history. It may push you to use the dark side of the --force
# method3 was called!
```

See, we can even manipulate classes using a class decorator! How awesome is that?

### Back to metaclasses

We already saw a very short use of metaclasses in the beginning of this post, but how are metaclasses **actually used** for metaprogramming in Python? Like, why would you use metaclasses or metaprogrammning?

To be really honest, for most things metaclasses will probably be an overkill :(. Remember "The Zen of Python" (aka PEP 20) creator's Tim Peter? He has a phamous phrase on metaclasses (but honestly, I couldn't track the source)

> “Metaclasses are deeper magic than 99% of users should ever worry about. If you wonder whether you need them, you don’t (the people who actually need them know with certainty that they need them, and don’t need an explanation about why).”
>
> Tim Peter

But of course, there are some cases in which metaclasses for metaprogramming are really nice to have, and I will make sure to list them:

#### Back to Design Patterns: the Singleton

OK. So you want to implement the Singleton pattern in Python. How do you do that? I've already saw some very clever uses of implementing singletons on a separate file - a module - and always importing from that file. But the problem is that this does not allow the Singleton to be lazily instantiated.

```python
#!/bin/env python3
# file: singleton.py

class Singleton:
    pass

singleton = Singleton()

# In other files you would do
from singleton import singleton

# Use the Singleton as you would like to
```

I really like this approach because "simple is better than complex", but let's go back one step and think about that from a metaprogramming point-of-view: we want to be able to, at runtime, always return the same object instead of creating a new one when instantiating a class....

```python
class SingletonMetaclass(type):
    """In order to create a metaclass, you must inherit from `type`"""
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            instance = super().__call__(*args, **kwargs)
            cls._instances[cls] = instance
        return cls._instances[cls]
    
    
class Singleton(metaclass=SingletonMetaclass):
    pass


singleton1 = Singleton()
singleton2 = Singleton()

assert singleton1 is singleton2, "Ooops, your singleton is bad and you should feel bad"
```

Awesome, we have created a metaclass that stores in a class variable class instances and retrieves it back if they already exists. Just what we want when thinking about Singletons! What is best is that this metaclass can be used for many different singletons!

#### ABC Metaclasses

Depending on your background, you must have already wondered about how one can create an interface with Python. To be honest, there is no such thing as an interface in Python, especially because we have things like *duck typing* and *multi inheritance*. But sometimes you want to make sure that some methods in a class are really implemented, for that we use `ABC` or [Abstract Base Classes](https://docs.python.org/3.7/library/abc.html).

```python
from abc import ABCMeta, abstractmethod


class Shape(metaclass=ABCMeta):
    @abstractmethod
    def area(self):
        pass

class Square(Shape):
    def __init__(self, size):
        self.size = size

square = Square(size=5)
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# TypeError: Can't instantiate abstract class Square with abstract methods area

class Square(Shape):
    def __init__(self, size):
        self.size = size
    def area(self):
        return self.size ** 2

square = Square(size=5)
```

See? Now users can't instantiate classes that inherit from the shape base class without implementing the area method!

#### Registering Objects

Suppose you have code to handle multiple DB connections using a common data structure (a dictionary), you can use metaclasses to automatically register new connectors!

```python
import inspect

_connectors = {}


def get_connection_handler_class(db):
    if db in _connectors:
        return _connectors[db]
    raise NotImplementedError(f"Could not find a connector for the {db}")


class RegisterMetaclass(type):
    """Automatically registers new db handler"""
    def __new__(meta, name, bases, attrs):
        # Create **instance class** of the db connector
        cls = type.__new__(meta, name, bases, attrs)
        # Registering new connector!
        _connectors[attrs["_db_connector"]] = cls
        return cls

   
class TeradataConnector(metaclass=RegisterMetaclass):
    _db_connector = "teradata"


class RedshiftConnector(metaclass=RegisterMetaclass):
    _db_connector = "redshift"


teradata = get_connection_handler_class(db="teradata")
assert inspect.isclass(teradata, TeradataConnector)


melilake = get_connection_handler_class(db="melilake")
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
#   File "<stdin>", line 4, in get_connection_handler_class
# NotImplementedError: Could not find a connector for the melilake
```

Awesome, mantaining this code now is a little easier, as we don't have to register the connectors ourselves, this happens automatically!

#### APIs

So, this is one of the nicest applications for metaprogramming and metaclasses. Usually, when creating a framework or a library you want to reduce boilerplate code and keep the a nice interface for your API. You can then, use metaclasses and metaprogramming to achieve this. Instead of writing code, let's see this technique in the wild:

##### Django ORM

If you already used Django you must know how clean and easy it is to define ORM models:

```python
from django.db import models


class Painting(models.Model):
    author = models.CharField(max_length=140)
    date = models.IntegerField()
    name = models.CharField(max_length=140)
    height = models.FloatField()
    width = models.FloatField()
    cost = models.FloatField()


painting = Painting(
    author="Vincent van Gogh",
    date=1889,
    name="The Starry Night",
    height=73.7,
    width=92.1
)

painting.author
# 'Vincent van Gogh'
```

You can find more details [here](https://github.com/django/django/blob/2.1.7/django/db/models/base.py#L61), but the interface for the user is so nice! At runtime, Django has to handle all sort of things like:

- Reading this data model class definition and preparing new, specialized classes to handle the functionality we need in an ORM
- Wiring all implicit and explict traversal relationship between different models
- Handles different types of model classes (proxy, abstracts, etc.)
- Handles all configurable metadata incoming from `settings.py` or the internal `Meta` class
- ...

There is a lot of things happening under the hood, but the interface for the user is so simple! That's the magic of using metaclasses and making use of metaprogramming. There is an awesome blog post about Django and metaclasses [here](https://medium.com/swlh/how-django-use-data-descriptors-metaclasses-for-data-modelling-14b307280fce).

### Domain Specific Languages (DSL)

Domain Specific Languages are widely used because they improve communication between developers and domain experts. In contrast to general programming languages (like Python and Java), DSLs are designed to solve a pretty specific problem. Some pretty nice examples of DSLs are SQL, XML, CSS, and HTML. You can even see the previous section on Django ORM as DSL!

A very nice and neat example is `argo-python-dsl` that is designed to help developers to programatically define Argo Workflows in Python which are then translated to Argo/Kubernetes YAML specifications.

```python
# Building a simple Workflow: one step
from argo.workflows.dsl import Workflow
from argo.workflows.dsl import template

from argo.workflows.dsl.templates import V1Container


class HelloWorld(Workflow):

    entrypoint = "whalesay"

    @template
    def whalesay(self) -> V1Container:
        container = V1Container(
            image="docker/whalesay:latest",
            name="whalesay",
            command=["cowsay"],
            args=["hello world"]
        )

        return container
```

The DSL then parses/compiles this class to this YAML:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  name: hello-world
  generateName: hello-world-
spec:
  entrypoint: whalesay
  templates:
  - name: whalesay
    container:
      name: whalesay
      image: docker/whalesay:latest
      command: [cowsay]
      args: ["hello world"]
```

Pretty neat, hm? You can find more complex examples on their [GitHub page](https://github.com/argoproj-labs/argo-python-dsl).

## Conclusion

Well, we saw a little bit on Python metaclasses, the `type` metaclass. and also a little bit on Metaprogramming use cases. I hope that was a good introduction on this subject and that you can make this knowledge to good use!

## References

Kansal, S. Metaprogramming in Python. Available at: https://developer.ibm.com/technologies/analytics/tutorials/ba-metaprogramming-python/

StackOverflow, What are metaclasses in Python. Available at: https://stackoverflow.com/questions/100003/what-are-metaclasses-in-python

Sturtz, J. Python Metaclasses. Available at: https://realpython.com/python-metaclasses/

Bennet, J. Contributing to classiness (in Django). Available at: https://www.b-list.org/weblog/2019/mar/04/class/
