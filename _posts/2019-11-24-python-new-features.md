---
layout: post
title: Python 3.8 New Features, The Walrus Operator And Positional-Only Parameters
category: Python
redirect_from: /2019/11/24/python-new-features/
---

<img src="/images/blog/2019-11/python-new-features.png" alt="Python New Features" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

# Section Overview

- The Walrus Operator
- Positional-Only Parameters
- References

## Section 1. The Walrus Operator (Assignment Expressions)

We use an assignment expression(*walrus operator*). It use the walrus operator (*:=*) to both assign and evaluate variable names in a single expression, thus reducing repetition. It is a new syntax introduced in **Python 3.8** and solve a long-standing problem with the language that can cause code duplication.

Now, let’s see:

```python
if fake_val := fake_func():
    # do something with fake_val
while fake_val := fake_func():
    # do something with fake_val
fake_val = fake_func1()
if not fake_val:
    logging.warning('fake func1')
    fake_val = fake_func2()
    if not fake_val:
        logging.warning('fake func2')
        val = func3()
if not fake_val:
    raise ValueError('....')
```

After upgrading to 3.8, you could refactor it to:

```python
if not (fake_val := fake_func1()):
    logging.warning('fake func1')
    if not (fake_val := fake_func2()):
        logging.warning('fake func2')
        if not (fake_val := fake_func3()):
            raise ValueError('....')
```

For example, Here, I define the contents of the basket.

```python
fruits_in_basket = {
    'apple': 15,
    'peach': 10,
    'lemon': 5,
}
```

Now we want to order some lemon. So we need to make sure there is at least one lemon in the basket.

```python
def order_func(count):
    ...
def out_of_stock():
    ...
count = fruits_in_basket.get('lemon', 0)
if count:
    order_func(count)
else:
    out_of_stock()
```

After upgrading to 3.8, you could refactor it to:

```python
if count := fruits_in_basket.get('lemon', 0):
    order_func(count)
else:
    out_of_stock()
```

If we want to do lemon juice, we need 10 lemon.

```python
def make_lemon_juice(count):
    ...
count = fruits_in_basket.get('lemon', 0)
if count >= 10:
    make_lemon_juice(count)
else:
    out_of_stock()
```

After upgrading to 3.8, you could refactor it to:

```python
if (count := fruits_in_basket.get('lemon', 0)) >= 10:
    make_lemon_juice(count)
else:
    out_of_stock()
```

If we want to order for peach and lemon juice;

```python
def make_peach_juice(count):
    ...
def make_lemon_juice(count):
    ...
count = fruits_in_basket.get('peach', 0)
if count >= 2:
    make_peach_juice(count)
else:
    count = fruits_in_basket.get('lemon', 0)
    if count >= 10:
        make_lemon_juice(count)
    else:
        # 'Nothing'
```

After upgrading to 3.8, you could refactor it to:

```python
if (count := fruits_in_basket.get('peach', 0)) >= 2:
    make_peach_juice(count)
elif (count := fruits_in_basket.get('lemon', 0)) >= 10:
    make_lemon_juice(count)
else:
    # 'Nothing'
```

In general, when you find yourself repeating the same expression or assignment multiple times within a grouping of lines, it’s time to consider using assignment expressions in order to improve readability.

## Section 2. Positional-Only Parameters

We can use `/` in a function definition to indicate that arguments before that are positional-only.

For example:


```python
def func(a, /):
    pass

func('foobar')
# This raises
func(a='foobar')
```

If the func uses **keyword argument** when **positional-only** is required, **TypeError** occurs.

Here’s an example function definition which includes all argument types.

```
def func(a, /, b, *, c):
    pass

a: positional-only
b: positional or keyword
c: keyword-only
```

Thus, **func** can be called like this:

```
func('foo', 'bar', c='foobar')
func('foo', b='bar', c='foobar')
```

But not like this:

```
# TypeError: func() got some positional-only arguments passed as keyword arguments: 'a'
func(a='foo', b='bar', c='foobar')

# TypeError: func() takes 2 positional arguments but 3 were given
func('foo', 'bar', 'foobar')
```

## Section 3. References

- https://www.python.org/dev/peps/pep-0569/#id6
- https://docs.python.org/3.8/whatsnew/3.8.html
- https://www.python.org/dev/peps/pep-0572/
- https://www.python.org/dev/peps/pep-0013/#electing-the-council
- https://docs.python.org/3/glossary.html#keyword-only-parameter
