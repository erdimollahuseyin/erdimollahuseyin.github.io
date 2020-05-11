---
layout: post
title: Python Tuples vs Lists - Comparison between tuples and lists
category: Python
redirect_from: /2020/04/21/python-tuples-and-lists/
---

<img src="/images/blog/2020-04/python-lists-tuples.png" alt="Python Tuples And Lists" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

In this article, we are going to try to explain review difference between tuples and lists. They are both similar sequence types in python. There is a big difference when considering lists and tuples. Tuples are immutable list but it isn’t true for lists. So, you cannot change their size as well as their immutable objects.

Tuples refer directly to their elements. Also, the lenght and order of objects are important. But lists have an extra layer of indirection to an external array of pointers. This provides speed advantage to tuple for indexed lookups and unpacking.

There are two benefits to using tuple.

- **Clarity**: When you see a tuple in the code, you know that lenght information will never change.
- **Performance**: A tuple uses less memory than a list of the same length.

*Example 1.* We can’t changes items in a tuple:

```
>>> a = (1, 'foo')
>>> a[0] = 10
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

*Example 2.* if there is any list(mutable) in a tuple, we can change it.

```
>>> a = (1, 'foo', [1, 2])
>>> b = (1, 'foo', [1, 2])
>>> a == b
True
>>> b[-1].append(3)
>>> a == b
False
>>> b
(1, 'foo', [1, 2, 3])
```

*Example 3.* A tuple’s size is fixed and don’t over-allocate. Thus, it can be stored more compactly than lists which need to over-allocate to make **append()** operations efficient.

```
>>> import sys
>>> a = (1, 2, 3)
>>> id(a)
4468514392
>>> sys.getsizeof(a)
72
>>> b = [1, 2, 3]
>>> id(b)
4469068104
>>> sys.getsizeof(b)
88
```

*Example 4.* Empty tuple always acts only one tuple as a singleton. It returns immediately itself. Also, It has a length of zero. When we create an empty tuple, points to the already preallocated one by python. Thus, both of them has the same address. They are saving memory.

```
>>> a = ()
>>> b = ()
>>> a is b
True
>>> id(a)
4390871112
>>> id(b)
4390871112
>>> a = (1, 2, 3)
>>> b = tuple(a)
>>> a is b
True
```

*Example 5.* Since tuples are **immutable**, they do not have to be **copied**. Lists are **mutable** objects and requires all the data to be **copied** to a new list:

```
>>> a = []
>>> b = []
>>> a is b
False
>>> id(a)
4504237576
>>> id(b)
4504282440
>>> a = [1, 2, 3] 
>>> b = list(a)
>>> a is b
False
```

## Where is tuple usually used ?

- string formatting.
- working with arguments and parameters.
- iterating over dictionary key-value pairs.
- returning 2 or more items from a function.

## Summary

In this article, we explained the tuple and list sequence types. Tuple(*Immutable*) sequence is more compact, faster and more to use. It can hold list(*Mutable*) objects and be sure to use these objects correctly with nested data structures. Although It cannot be changed, it can change if it contains a list(*Mutable*) object.

## References

- https://docs.python.org/3/library/collections.html
- https://realpython.com/python-lists-tuples/