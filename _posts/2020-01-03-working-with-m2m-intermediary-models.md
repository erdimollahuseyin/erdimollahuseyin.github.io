---
layout: post
title: Working with M2M Intermediary Models
category: Django
redirect_from: /2020/01/03/working-with-m2m-intermediary-models/
---

<img src="/images/blog/2020-01/m2m-1.jpeg" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

In this article, we will learn how to use **through** for Django many-to-many relationship. A few times in the last month, I’ve run into the need to add some metadata to a Django many-to-many relationship.

Let’s go **through** an example the existing models:

<img src="/images/blog/2020-01/m2m-2.png" alt="Person and Group Model" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Create a model that matches the existing **through** table and using **Meta.db_table**:

<img src="/images/blog/2020-01/m2m-3.png" alt="Membership Model" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Now, we change the many-to-many relationship to use the new model:

<img src="/images/blog/2020-01/m2m-4.png" alt="Group Model" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Now generate a new migration with **python manage.py makemigrations**. Then we will change it.

`The initial migration`:

<img src="/images/blog/2020-01/m2m-5.png" alt="Migration File" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

We just need to wrap these operations in **migrations.SeparateDatabaseAndState** to get the models in sync without screwing up the existing database setup. All of the changes above represent state changes:

<img src="/images/blog/2020-01/m2m-6.png" alt="Migration File" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Now you can add fields and create additional migrations as normal.

## Admin

Admin should look like this:

<img src="/images/blog/2020-01/m2m-7.png" alt="Person, Group and Membership Admin" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

<img src="/images/blog/2020-01/m2m-8.png" alt="Person Admin" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

<img src="/images/blog/2020-01/m2m-9.png" alt="Group Admin" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

<img src="/images/blog/2020-01/m2m-10.png" alt="Group Admin" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Filtering on through fields

You should be able to filter on the **through** table fields by using the lowercase name of the **through** model.

```python
# querysets
Group.objects.filter(membership__invite_reason="foo")
<QuerySet [<Group: Python>]>

Group.objects.filter(membership__invite_reason="bar")
<QuerySet [<Group: Django>]>

Person.objects.filter(membership__invite_reason="foo")
<QuerySet [<Person: Erdi>]>

Person.objects.filter(membership__invite_reason="bar")
<QuerySet [<Person: Erdi>]>

Membership.objects.filter(invite_reason='foo')
<QuerySet [<Membership: Erdi Python>]>

Membership.objects.filter(invite_reason='bar')
<QuerySet [<Membership: Erdi Django>]>

# related manager on instances
person_instance = Person.objects.first()
person_instance.groups.filter(membership__invite_reason='foo')
<QuerySet [<Group: Python>]>
    
group_instance = Group.objects.first()
group_instance.members.filter(membership__invite_reason='foo')
<QuerySet [<Person: Erdi>]>
```

In this article you’ve learned how to use a **through** with many-to-many relationship. If you want to see more information on **through**, you can visit [this](https://docs.djangoproject.com/en/3.0/topics/db/models/).
