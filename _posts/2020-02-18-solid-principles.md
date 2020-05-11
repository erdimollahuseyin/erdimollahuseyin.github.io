---
layout: post
title: The SOLID Principles
category: Python
redirect_from: /2020/02/18/the-solid-principles/
---

<img src="/images/blog/2020-02/solid.jpeg" alt="SOLID Principles" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

In this article, I’m going to try to explain review the so-called **SOLID Principles**, and how to implement them in a Pythonic way. We will learn how to develop maintainable. software systems applying design patterns on **SOLID Principles**.

SOLID is the basic principle of OOP(Object-Oriented Programming), which was defined in the early 2000s introduced by **Michael Feathers** and named by the First Five Principles, **Robert C. Martin**. When talking about the SOLID Principles, reference is made to **classes** and **modules**. This does not mean that they apply only to OOP, though they do work very well with OOP.

OOP is about managing dependencies by inverting key dependencies to prevent **rigid code, fragile code**, and **non-reusable code**. There are three terms to describe non-reusable code:

- **Rigidity**: When a change to one part of a program can break another part
- **Fragility**: When things break in unrelated places
- **Immobility**: When code cannot be reused outside its original context

We can solve these problems with **SOLID Principles**. It is considered as the basic principle of Object Oriented Programming (OOP) and defines **five** design principles.

These principles make software design more understandable, flexible, maintainable.

- **S**ingle-Responsibility Principle (*SRP*)
- **O**pen-Closed Principle (*OCP*)
- **L**iskov Substitution Principle (*LSP*)
- **I**nterface Segregation Principle (*ISP*)
- **D**ependency Inversion Principle (*DIP*)

Let’s take a look at these principles one by one.

## Single-Responsibility Principle (SRP)

**This principle is often stated as**:

```
A software component(Module or Class) must have only one responsibility.
```

Each class in a program should focus on a single task. Thus, don’t put methods that change for different reasons in the same class. We can conclude that it must have only one reason to change.

In this example, There is a **Circle** class. It provides an method called **draw()**.

```python
class Circle():
    def draw(self):
        """Draw a circle"""
        pass
```

## Open-Closed Principle(OCP)

**This principle is often stated as**:

```
A software entities (source file, module, class, 
or function) should be open for extension but closed for modification.
```

What it means is that we should be able to add functionality without having to rewrite everything from scratch.

In this example, we get an app that draws **Circles** and **Squares**. While doing the drawing, draw like this if the type is a **circle** or **square**. But we break this principle if we want to draw according to triangle in the future.

```python
class Shape:
    def draw_circle(self):
        """Draw a circle"""
        pass
    def draw_square(self):
        """ Draw a square"""
        pass
    def draw_triangle(self):
        """ Draw a triangle"""
        pass
```

The following code snippet was written in accordance with this principle, considering this situation.

```python
class Shape
    def draw(self):
        """Draw a shape"""
        pass
class Circle(Shape):
    def draw(self):
        """Draw a circle"""
        pass
class Square(Shape):
    def draw(self):
        """Draw a square"""
        pass
```

## Liskov Substitution Principle(LSP)

**This principle is often stated as**:

```
If for each object o1 of type S there is an object o2 of type T 
such that for all programs P defined in terms of T, the 
behavior of P is unchanged when o1 is substituted for o2 
then S is a subtype of T.

Barbara Liskov, “Data Abstractions and Hierarchy”, 1988.
```

The Liskov Substitution Principle states that the design must provide the ability to replace any instance of a parent class with an instance of one of its child classes. If a parent class can do something, a child class must also be able to do it.

In this example, Vehicle class has an engine method. But the bicycle not has engine.

```python
class Vehicle:
    def __init__(self, name):
        self.name = name
    def engine(self):
        """A Vehicle engine"""
        pass
    def get_name(self):
        """Get vehicle name"""
        return f'The vehicle name {self.name}'
class Bicycle(Vehicle):
    def engine(self):
        pass
class Car(Vehicle):
    def engine(self):
        pass
```

So we change the code according to this rule.

```python
class Vehicle:
    def __init__(self, name):
        self.name = name
    def get_name(self):
        """Get vehicle name"""
        return f'The vehicle name {self.name}'
class VehicleWithoutEngine(Vehicle):
    pass
class VehicleWithEngine(Vehicle):
    def engine(self):
        """A Vehicle engine"""
        pass
class Bicycle(VehicleWithoutEngine):
    pass
class Car(VehicleWithEngine):
    def engine(self):
        """A vehicle engine"""
        pass
```
## Interface Segregation Principle(IPS)

**This principle is often stated as**:

```
A client should not be forced to implement an interface that it does not use.
```

The Interface Segregation Principle(ISP) states that it is better to have many small interfaces than a few larger ones and provides some guidelines over an idea that we have revisited quite repeatedly already: that interfaces should be small.

In object-oriented terms, an interface is represented by the set of methods an object exposes. This is to say that all the messages that an object is able to receive or interpret constitute its interface, and this is what other clients can request. The interface separates the definition of the exposed behavior for a class from its implementation.

In this example, We call some methods from Circle and Square class. But they are unnecessary methods.

```python
class Shape:
    def draw_circle(self):
        """Draw a circle"""
        pass
    def draw_square(self):
        """Draw a square"""
        pass
    
class Circle(Shape):
    def draw_circle(self):
        """Draw a circle"""
        pass
    def draw_square(self):
        """Draw a square"""
        pass
class Square(Shape):
    def draw_circle(self):
        """Draw a circle"""
        pass
    def draw_square(self):
        """Draw a square"""
        pass
```

The following code snippet was written in accordance with this principle, considering this situation.

```python
class Shape:
    def draw(self):
        """Draw a shape"""
        pass
class Circle(Shape):
    def draw(self):
        """Draw a circle"""
        pass
class Square(Shape):
    def draw(self):
        """Draw a square"""
        pass
```

## Dependency Inversion Principle(DIP)

**This principle is often stated as**:

```
High-level modules should not depend on low-level 
modules. Both should depend on abstractions. Abstractions 
should not depend on details. Details should depend on 
abstractions.
Or, source code dependencies should refer only to 
abstractions, not to concretions.
```

Abstractions have to be organized in such a way that they do not depend on details, but rather the other way around — the details (concrete implementations) should depend on abstractions.

Imagine that two objects in our design need to collaborate, A and B. A works with an instance of B, but as it turns out, our module doesn’t control B directly (it might be an external library, or a module maintained by another team, and so on). If our code heavily depends on B, when this changes the code will break. To prevent this, we have to invert the dependency: make B have to adapt to A. This is done by presenting an interface and forcing our code not to depend on the concrete implementation of B, but rather on the interface we have defined. It is then B’s responsibility to comply with that interface.

In this example, The **Sports** class is a High-level module. The **Football** and **Basketball** are the Low-level modules.

```python
class Football:
    """Low-level module"""
    @staticmethod
    def play_football():
        print('play football')
class Basketball:
    """Low-level module"""
    @staticmethod
    def play_basketball():
        print('play basketball')
class Sports:
    """High-level module"""
    def __init__(self):
        self.football = Football()
        self.basketball = Basketball()
    def playing(self):
        self.football.play_football()
        self.basketball.play_basketball()
        return 'Playing...'
```

**Result**:

```
In [1]: s = Sports()
In [2]: s.playing()
play football
play basketball
Out[2]: 'Playing...'
```

Let’s solve the problem according to **Dependency Inversion Principle**.

```python
class Football:
    """Low-level module"""
    def playing(self):
        self.__play_football()
    @staticmethod
    def __play_football():
        print('play football')
class Basketball:
    """Low-level module"""
    def playing(self):
        self.__play_basketball()
    @staticmethod
    def __play_basketball():
        print('play basketball')
class Playing:
    """Abstract module"""
    def __init__(self):
        self.football = Football()
        self.basketball = Basketball()
    def playing(self):
        self.football.playing()
        self.basketball.playing()
class Sports:
    """High-level module"""
    def __init__(self):
        self.__playing = Playing()
    def plays(self):
        return self.__playing.playing()
```

**Result**:

```
In [1]: sport = Sports()
In [2]: sport.plays()
play football
play basketball
```

## Summary
In this article, we have explored the **SOLID Principles** with the goal of understanding clean design. These Principles are not a magic rule, but they provide good guidelines to follow that have been proven to work in past projects and will make our software much more likely to succeed and one of the most influential sets of object-oriented guidelines used today.

## References

- [SingleReponsibilityPrinciple](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html)
- [pep-3119](https://www.python.org/dev/peps/pep-3119/)
