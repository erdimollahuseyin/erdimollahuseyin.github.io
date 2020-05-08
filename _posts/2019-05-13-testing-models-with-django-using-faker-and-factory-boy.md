---
layout: post
title: Testing models with Django using Faker and Factory Boy
category: Django
redirect_from: 2019-05-13-testing-models-with-django-using-faker-and-factory-boy/
---

<img src="/images/blog/2019-05/testing-models-with-django-using-faker-and-factory-boy.jpeg" alt="Testing models with Django using Faker and Factory Boy" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

The factory_boy library(modeled after Factory Girl in Rails) the generation of data for tests. 
In Django, data for tests was called fixtures, loaded from files in the code.

We can install this library:

```
$ pipenv install factory_boy
```

Then I will create a structure like this inside my app:

```
$ core/
  ----tests/
  ---------/__init__.py
  ---------factories/
  ------------------/__init__.py
  ------------------/company.py
  ---------/test_models/
  --------------------/__init__.py
  --------------------/test_company.py
```

The **factories folder** contains the factory company that will create the company object in the testing phase. 
While the **test_company.py** file contains the test code for the **company**.

# Using Factories

- create an object through the ORM.
- use the **Meta class** to tell your factory which model to use.

We will create company factory in **core/factories/company.py**:

```python
import factory
class CompanyFactory(factory.DjangoModelFactory):
    """
       | id  | name      |
       |:----|:----------|
       |  1  | X Company |
    """
    class Meta:
        model = 'core.Company'
        django_get_or_create = ('name',)
    name = 'company name'
```

The factory’s Meta has `django_get_or_create` set, which means the factory will call the Django built-in `Company.objects.get_or_create` method.

Now, just calling **CompanyFactory.build()** will generate a filled **company** instance:

```
# Build a Company instance and override company
>>> company_obj = CompanyFactory.build(name='X Company')
>>> company_obj.name
    "X Company"
```

Now creating and having a company on your database is simple as writing **CompanyFactory.create()**:

```
# Returns a saved Company instance
>>> company = Company.create(name='X Company')
# or
# Same as CompanyFactory.create()
>>> company = CompanyFactory(name='X Company')
```

You can access the object within your tests:

```python
from test_factories import CompanyFactory
company = CompanyFactory()
```

You can pass a different value in when you use the factory:

```python
company = CompanyFactory(name='Y Company')
```

Our company instance will have a name of **Y Company** instead of **company name**.

We want to create more objects:

```
>>> companies = Company.create_batch(3, name=factory.Sequence(lambda n: 'Name {0}'.format(n)))
>>> len(companies)
    3
>>> [company.name for company in companies]
    ["Name 1", "Name 2", "Name 3"]
```

Factories are classes used to generate data for specific models. These are called by the tests.

This is our updated `main/tests/test_models/company.py`:

```python
from django.test import TestCase
from ..factories import CompanyFactory
class CompanyModelsTestCase(TestCase):
    @classmethod
    def setUpTestData(cls):
        cls.Company = CompanyFactory._meta.model
    def test_crud_company(self):        
        # create
        company = CompanyFactory(name='X Company')
        # read
        self.assertEqual(company.name, 'X Company')
        self.assertEqual(company.pk, company.id)
        self.assertQuerysetEqual(
            self.Company.objects.all(),                 
            ['<Company: X Company>']
        )
        # update
        company.name = 'Y Company'
        company.save()
        self.assertQuerysetEqual(
            self.Company.objects.all(),  
            ['<Company: Y Company>']
        )
```

# Features of Factory Boy

- Use a **Sequence** object using a lambda function to dynamically create unique field values. For example;

```python
name = factory.Sequence(lambda n: ‘company_%d’ % n)`
```

- Use a **LazyAttribute** object to return a value for a defined field. For example;

```python
slug = factory.LazyAttribute(lambda o: o.name)
```

- Subfactories
- Many-to-many relationship

## Sequences

If you want to create multiple instances of a given object and unique attribute,
you can add **factory.Sequence**.

```python
import factory
class CompanyFactory(factory.DjangoModelFactory):
    """
       | id  | name      |
       |:----|:----------|
       |  1  | X Company |
    """
    class Meta:
        model = 'core.Company'
        django_get_or_create = ('name',)
    name = factory.Sequence(lambda n: 'Company %d' % n)
```

When this factory is called, create a unique company each time, **n** is incerementing automatically.

## Lazy Attributes

**o** is the instance being constructed within the factory.
You can use string formatting, with some attribute.

```python
import factory
class CompanyFactory(factory.DjangoModelFactory):
    """
       | id  | name      |
       |:----|:----------|
       |  1  | X Company |
    """
    class Meta:
        model = 'core.Company'
        django_get_or_create = ('name',)
    name = factory.Sequence(lambda n: 'Company %d' % n)
    slug = factory.LazyAttribute(lambda o: o.name)
```

## SubFactories

I needed to define a factory for testing in a Django app. 
The models had a one-to-many relationship, e.g. **company** has many **contracted companies**. 
Factory Boy allows you to use sub-factories within your factories. 
**SubFactory** is used for FK fields.

```python
import factory
class CompanyFactory(factory.DjangoModelFactory):
    """
       | id  | name      |
       |:----|:----------|
       |  1  | X Company |
    """
    class Meta:
        model = 'core.Company'
        django_get_or_create = ('name',)
    name = factory.Sequence(lambda n: 'Company %d' % n)
    slug = factory.LazyAttribute(lambda o: o.name)
class ContractedCompanyFactory(factory.DjangoModelFactory):
    """
        | id  | start_date      | end_date      | company |
        |:----|:--------------------------------|:--------|
        |  1  | 2019-05-13      | 2020-05-13     | 1      |
    """
    class Meta:
        model = 'core.ContractedCompany'
    start_date = parse_date('2019-05-13')
    end_date = parse_date('2020-05-13')
    company = factory.SubFactory(CompanyFactory)
```

I will create a contracted company with in my tests:

```python
contracted_company = ContractedCompanyFactory()
```

## Many-to-many relationship

We use base building block for this feature is the **post_generation**:

```python
class CompanyFactory(factory.DjangoModelFactory):
    """
       | id  | name      |
       |:----|:----------|
       |  1  | X Company |
    """
    class Meta:
        model = 'core.Company'
        django_get_or_create = ('name',)
    name = factory.Sequence(lambda n: 'Company %d' % n)
    slug = factory.LazyAttribute(lambda o: o.name)
    @factory.post_generation
    def phones(self, create, extracted):
        if not create:
            return
        if extracted:
            for phone in extracted:
                self.phones.add(phone)
    @factory.post_generation
    def emails(self, create, extracted):
        if not create:
            return
        if extracted:
            for email in extracted:
                self.emails.add(email)
    @factory.post_generation
    def addresses(self, create, extracted):
        if not create:
            return
        if extracted:
            for address in extracted:
                self.addresses.add(address)
```

When calling **CompanyFactory()** or **CompanyFactory.build()**, **no phones** or **no emails** or **no addresses** binding will be created. But when **CompanyFactory.create(phones=(phone1, phone2, phone3), .....)** is called, the phones declaration will add passed in phones to the set of phones for the company.

---------

# Example: Fakes And Factories

- **faker**: generate fake data
- **factory_boy**: create factories of our models

In our **Django** apps, we have the MTV(Model Template View) structure. Firstly, we have to create models for tests. If you want to generate random strings or generate unique instances of our models, we need to use them.

## Start Django Project
We have created a simple **Django** app. Look in our **models.py** file:

```python
from django.contrib.auth import get_user_model
from django.db import models
User = get_user_model()
class NewsContent(models.Model):
    headline = models.CharField(max_length=255)
    body = models.TextField()
    author = models.ForeignKey(
        to=User,
        on_delete=models.CASCADE,
        related_name='contents_by_author'
    )
    def __str__(self):
        return self.headline
```

## Functionality

Now, we have some custom logic in our system. That’s right! It’s time for adding some tests. Look in our **views.py** file:

```python
from django.http import HttpResponseNotAllowed
from django.shortcuts import render

from .models import NewsContent


def news_content_list(request, author_id):
    if request.method == 'GET':
        object_list = NewsContent.objects.filter(
            author__id=author_id
        )
        return render(request, 'news_content_list.html', locals())
    return HttpResponseNotAllowed(['GET'])
```

Look in our `news_content_list.html` file:

```
{% for news_content in object_list %}
   <ul>
        <li>
            {{ news_content.headline }}, {{ news_content.author }}
        </li>
        <li>{{ news_content.body }}</li>
    </ul>
{% endfor %}
```

## Plan Our Testing

Let’s add our first test. If we have 5 news contents by a single author we have to assure that the view lists them all.

```python
# in tests/test_views.py
from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse

from .models import NewsContent

User = get_user_model()


class NewsContentListViewTests(TestCase):
    def setUp(self):
        self.author = User.objects.create_user(
            'john', 'lennon@thebeatles.com'
        )
        self.news_content = NewsContent.objects.create(
            headline='Real Madrid frustrated by Athletic',
            body='Real Madrid hit the woodwork three times',
            author=self.author
        )
        self.url = reverse(
            'news:news_content_list', 
             args=(self.author.id,)
        )

    def test_with_several_news_content_by_one_user(self):
        news1 = NewsContent.objects.create(
            headline='Test Title 1',
            body='Test Body 1',
            author=self.author
        )
        news2 = NewsContent.objects.create(
            headline='Test Title 2',
            body='Test Body 2',
            author=self.author
        )
        news3 = NewsContent.objects.create(
            headline='Test Title 3',
            body='Test Body 3',
            author=self.author
        )
        news4 = NewsContent.objects.create(
            headline='Test Title 4',
            body='Test Body 4',
            author=self.author
        )
        news5 = NewsContent.objects.create(
            headline='Test Title 5',
            body='Test Body 5',
            author=self.author
        ) 

        response = self.client.get(self.url)

        self.assertEqual(200, response.status_code)
        self.assertContains(response, news1.headline)
        self.assertContains(response, news2.headline)
        self.assertContains(response, news3.headline)
        self.assertContains(response, news4.headline)
        self.assertContains(response, news5.headline)
```

## Let’s start refactoring

First, why using **faker** that is a **Python** package that generates fake data for you. How our tests are **changing** to look like:

```python
# in tests/test_views.py
from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse
from faker import Factory

from .models import NewsContent

User = get_user_model()


faker = Factory.create()


class NewsContentListViewTests(TestCase):
    def setUp(self):
        self.author = User.objects.create_user(
                          faker.name(), 
                          faker.email()
                      )
        self.news_content = NewsContent.objects.create(
            headline=faker.word(),
            body=faker.text(),
            author=self.author
        )
        self.url = reverse(
            'news:news_content_list', 
             args=(self.author.id,)
        )

    def test_with_several_news_content_by_one_user(self):
        news1 = NewsContent.objects.create(
            headline=faker.word(),
            body=faker.text(),
            author=self.author
        )
        news2 = NewsContent.objects.create(
            headline=faker.word(), 
            body=faker.text(), 
            author=self.author
        )
        news3 = NewsContent.objects.create(
            headline=faker.word(), 
            body=faker.text(), 
            author=self.author
        )
        news4 = NewsContent.objects.create(
            headline=faker.word(), 
            body=faker.text(), 
            author=self.author
        )
        news5 = NewsContent.objects.create(
            headline=faker.word(), 
            body=faker.text(), 
            author=self.author
        )

        response = self.client.get(self.url)

        self.assertEqual(200, response.status_code)
        self.assertContains(response, news1.headline)
        self.assertContains(response, news2.headline)
        self.assertContains(response, news3.headline)
        self.assertContains(response, news4.headline)
        self.assertContains(response, news5.headline)
```

If you want to learn more useful methods about faker, you can visit [it](https://faker.readthedocs.io/en/master/providers.html). 
We have the same test functionality as before. 
Faker is randomly **generating values**. Now create some factories. Factories are **Python** classes and write to your database as Django models do.

```
Model.objects.create()
or
ModelFactory()
```

Let’s create factories for our models:

```python
import factory
from django.contrib.auth import get_user_model
from faker import Factory
from my_news.news.models import NewsContent
User = get_user_model()
faker = Factory.create()
class UserFactory(factory.DjangoModelFactory):
    class Meta:
        model = User
    name = faker.name()
    email = faker.email()
class NewsContentFactory(factory.DjangoModelFactory):
    class Meta:
        model = NewsContent
    headline = faker.word()
    body = faker.text()
    author = factory.SubFactory(UserFactory)
```

- **class Meta**: define the model of your factory
- **faker** : generate random values
- `factory.SubFactory(NewsContentFactory) == models.ForeignKey(NewsContent)`

```python
# in tests/test_views.py
from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse
from faker import Factory

from .factories import NewsContentFactory, UserFactory
from .models import NewsContent

User = get_user_model()


faker = Factory.create()


class NewsContentListViewTests(TestCase):
    def setUp(self):
        self.author = UserFactory()
        self.news_content = NewsContentFactory(author=self.author)
        self.url = reverse(
            'news:news_content_list', 
             args=(self.author.id,)
        )

    def test_with_several_news_content_by_one_user(self):
        news1 = NewsContentFactory(author=self.author)
        news2 = NewsContentFactory(author=self.author)
        news3 = NewsContentFactory(author=self.author)
        news4 = NewsContentFactory(author=self.author)
        news5 = NewsContentFactory(author=self.author)

        response = self.client.get(self.url)

        self.assertEqual(200, response.status_code)
        self.assertContains(response, news1.headline)
        self.assertContains(response, news2.headline)
        self.assertContains(response, news3.headline)
        self.assertContains(response, news4.headline)
        self.assertContains(response, news5.headline)
```

## DRY(Don’t Repeat Yourself) — Factories

Factories are behaving like objects from the ORM.

- **create batch**: create a batch of objects without repeating our code.

```python
# in tests/test_views.py
from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse
from faker import Factory

from .factories import NewsContentFactory, UserFactory
from .models import NewsContent

User = get_user_model()


faker = Factory.create()


class NewsContentListViewTests(TestCase):
    def setUp(self):
        self.author = UserFactory()
        self.news_content = NewsContentFactory(author=self.author)
        self.url = reverse(
            'news:news_content_list', 
             args=(self.author.id,)
        )

    def test_with_several_news_content_by_one_user(self):
        news_contents = NewsContentFactory.create_batch(
                             5, 
                             author=self.author
                        )

        response = self.client.get(self.url)
        self.assertEqual(200, response.status_code)
        for news_content in news_contents:
            self.assertContains(response, news_content.headline)
```

# Conclusion

In this article, we learned how to use faker and factories. They are very helpful for model tests. If you want to see more information on `factory_boy`, you can visit [this](https://factoryboy.readthedocs.io/en/latest/index.html).
