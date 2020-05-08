---
layout: post
title: Generic relations to your models
category: Django
redirect_from: 2019-04-23-generic-relations-to-your-models/
---

<img src="/images/blog/2019-04/generic-relation.jpeg" alt="Generic Relation" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

In generic relations, **ContentType** objects that are used for the relationship. 
You will need three fields for a generic relation in a model:

- A **ForeignKey** field to **ContentType**: The model for the relationship.
- The primary key of the related object: **PositiveIntegerField** to match Django’s automatic primary key fields.
- A field to define and manage the generic relation using the two previous fields: **GenericForeignKey** (content types framework).

Edit the **models.py** file of the location application and make it look like this:

```python
from django.contrib.contenttypes.fields import GenericForeignKey
from django.contrib.contenttypes.models import ContentType
from django.db import models
class Location(models.Model):
    name = models.CharField(max_length=255)
    limit = models.Q(app_label='core', model='user')
    location_type_name = models.ForeignKey(
        ContentType,
        blank=True,
        null=True,
        related_name='location_type_obj',
        on_delete=models.CASCADE,
        limit_choices_to=limit,
    )
    location_type_id = models.PositiveIntegerField(
        null=True,
        blank=True,
        db_index=True
    )
    location_type = GenericForeignKey(
        'location_type_name',
        'location_type_id'
    )
    ......
```

We have added the following fields to the **Location** model:

- `location_type_name`: A FK Field (*the ContentType model*).
- `location_type_id`: A PositiveIntegerField for primary key of the related object.
- `location_type`: A GenericForeignKey field to the related object based on the combination of the two previous fields.

Django does not create any field in the database for **GenericForeignKey** fields. 
The only fields that are mapped to database fields are `location_type_name` and `location_type_id`. 
Both fields have blank=True and null=True attributes so that a location_type object is not required when saving **Location** objects.
Run the following command:

```
python manage.py makemigrations
```

You should see the following output:

```
Migrations for 'locations':
  locations/migrations/0001_initial.py
    - Create model Location
```

Then, run the next command to sync the application with the database:

```
python manage.py migrate
You should see the following output:
Applying locations.0001_initial... OK
```

Let’s add the **Location** model to the administration site.
Edit the **admin.py** file of the **locations** application and add the following code to it:

```python
from django.contrib import admin
from .models import Location
@admin.register(Location)
class LocationAdmin(admin.ModelAdmin):
    list_display = ('name',)
    search_fields = ('name',)
```

You just registered the **Location** model in the administration site.
Run the **python manage.py runserver** command to initialize the development server and open in your browser (*http://127.0.0.1:8000/admin/locations/location/add/*).

As you would notice, only the`location_type_name` and `location_type_id` fields that are mapped to actual database fields are shown. The **GenericForeignKey** field does not appear in the form.

The `location_type_name` field allows you to select any of the registered models of your Django project. You can restrict the content types to choose from a limited set of models using the `limit_choices_to` attribute in the `location_type_name` field: the `limit_choices_to` attribute allows you to restrict the content of FK fields to a specific set of values.
