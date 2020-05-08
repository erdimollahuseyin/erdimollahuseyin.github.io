---
layout: post
title: Modified Preorder Tree Traversal in Django
category: Django
redirect_from: /2019/08/10/modified-preorder-tree-traversal-in-django/
---

<img src="/images/blog/2019-08/mptt.jpeg" alt="Modified Preorder Tree Traversal" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

In this article, we will learn how to use django-mptt in a Django site.
MPTT is [modified preorder tree traversal](https://django-mptt.readthedocs.io/en/latest/). 
It’s an efficient way to store hierarchical data to your Django models. You easily determine parent-child relationships.

# Installation

```
$ pip install django-mptt
```

You should add **mptt** to the **INSTALLED_APPS** in your **settings.py** file:

```python
INSTALLED_APPS = (
    'django.contrib.auth',
    # ...
    'mptt',
)
```

I’ve used MPTT in one of my major projects. Consider following models for app in Django project.

# Example Models

```python
from django.db import models
from mptt.models import MPTTModel, TreeForeignKey
class LocationType(models.Model):
  
  name = models.CharField(max_length=50, unique=True)
  ....
class Location(MPTTModel):  
  parent = TreeForeignKey(
       to='self',
       related_name='children',
       null=True,
       blank=True,
       on_delete=models.CASCADE,
  )
  location_type = models.ForeignKey(
      to='LocationType', 
      related_name='locations',         
      on_delete=models.CASCADE
  )
  name = models.CharField(max_length=255)
  ....
```

We firstly add imports for the **MPTTModel** and **TreeForeignKey** classes from the django-mptt library. 
**MPTTModel** provides a base model. Then the **LocationType** and **Location** classes are defined. 
These models will also have a number of other fields: **level, lft, rght,** and **tree_id**.

- **level**: The level (or “depth”) at which a node sits in the tree.
- **lft**
- **rght**
- **tree_id**: A unique identifier assigned to each root node and inherited by all its descendants.

It also provides subclasses of MPTTModel have the following instance methods:

- `get_ancestors(ascending=False, include_self=False)`
- `get_children()`
- `get_descendants(include_self=False)`
- `get_descendant_count()`
- `get_family()`
- `get_next_sibling()`
- `get_previous_sibling()`
- `get_root()`
- `get_siblings(include_self=False)`
- `insert_at(target, position=’first-child’, save=False)`
- `is_child_node()`
- `is_leaf_node()`
- `is_root_node()`
- `move_to(target, position=’first-child’)`

According to me, the most important methods are `move_to(), get_root(), insert_at()`. 
These methods are particularly helpful. 
We change manually lft and rght values. 
But it’s not a good idea. 
I think, you can use `insert_at()`, if you want to update the tree with safe way. 
Also, we can replace manually parent and child node with `move_to()`.

Now create and apply the migrations to create the table in the database:

```
$ python manage.py makemigrations
Migrations for 'src':
  src/migrations/0001_initial.py
    - Create model LocationType
    - Create model Location
```

Now, We wanto to test the **Tree** from **shell**:

```
$ python manage.py shell
Python 3.7.0 (default, Apr  1 2019, 14:21:55)
In[1]: from src.models import Location, LocationType
In[2]: continent_obj = LocationType.objects.create(name='Continent')
In[3]: country_obj = LocationType.objects.create( name='Country') In[4]: city_obj = LocationType.objects.create(name='City')
In[5]: continent_location = Location.objects.create(
.....:   location_type =continent_obj, 
.....:   name='European'
.....: )
In[6]: country_location = Location.objects.create(
.....:   parent=continent_location,
.....:   location_type =country_obj, 
.....:   name='Turkey'
.....: )
In[7]: city_location = Location.objects.create(
.....:   parent=country_location,
.....:   location_type =city_obj, 
.....:   name='Istanbul'
.....: )
```

**Result for Location Type**:

```
| id  | name      |
|:----|:----------|
|  1  | Continent |
|  2  | Country   |
|  3  | City      |
```

**Result for Location**:

```
| id  | parent_id | location_type_id |  name        |
|:----|:----------|:-----------------|:-------------|
|  1  | None      |        1         |  European    |
|  2  | 1         |        2         |  Turkey      |
|  3  | 2         |        3         |  Istanbul    |
```

We want to get a listing of child locations for European:

```
In[8]: continent_location.get_descendants()
Out[8]: <TreeQuerySet [<Location: Turkey>, <Location:Istanbul>]>
In[9]: continent_location.get_descendant_count()
Out[9]: 2
```

We want to get a listing of parent locations for Istanbul:

```
In[10]: city_location.get_ancestors(include_self=True)
Out[10]: <TreeQuerySet [<Location: European>, <Location: Turkey>, <Location: Istanbul>]>
```

The root node of the MPTT tree (Istanbul):

```
In [11]: city_location.get_root()
Out[11]: <Location: European>
```

We want to see all locations on the same level as another:

```
In [12]: Location.objects.create(
.......:   location_type=continent_obj,
.......:   name='Asia'
.......: )
Out[12]: <Location: Asia>
In [13]: continent_location.get_siblings()
Out[13]: <TreeQuerySet [<Location: Asia>]>
```

Level of all locations:

```
In [14]: continent_location.level
Out[14]: 0
In [15]: country_location.level
Out[15]: 1
In [16]: city_location.level
Out[16]: 2
```

# References

- https://github.com/django-mptt/django-mptt
- https://django-mptt.readthedocs.io/en/latest/
