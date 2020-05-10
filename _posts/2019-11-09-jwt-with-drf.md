---
layout: post
title: JWT(JSON Web Token) With DRF
category: Django Rest Framework
redirect_from: /2019/11/09/jwt-with-drf/
---

<img src="/images/blog/2019-11/jwt.jpeg" alt="Jwt" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

# Section Overview

- build django project.
- define the news content model and admin.
- configure news content api.
- authentication and permissions.
- adding JWT(JSON Web Token).
- swagger docs.

[JSON Web Token](https://jwt.io) is an Token based authentication used by client/server applications where the client is a web, native, mobile or something else application using. JWT is a popular implementation of token based authentication.

In this article we’ll use it to authenticate users that JWT using with [Django REST Framework](https://www.django-rest-framework.org). 
When the news content is created, only current logged in a user can perform read and write operations to his own news contents.

## Section 1. Build Django Project

Firstly, install **Django, Django REST Framework(DRF)** and **django-cors-headers(CORS)**.

```
$ pip install django django-rest-framework django-cors-headers
```

Then, create a new project folder (*news_contents/*).

```
$ mkdir news_contents
$ cd news_contents
```

Create venv(*virtual environment*) and activate it.

```
$ python -m venv venv
$ source venv/bin/activate
```

Now, build a django project (*core*).

```
$ django-admin startproject core
$ cd core

```

Create a **newscontents** app.

```
$ python manage.py startapp newscontents
$ python manage.py migrate
```

We will now configure our django project to use **CORS** and **DRF** in `core/settings.py`.

```python

# Application definition
INSTALLED_APPS = [
    ....
    'rest_framework',
    'corsheaders',
    'newscontents',
]
MIDDLEWARE = [
    ....
    'corsheaders.middleware.CorsMiddleware',
]
CORS_ORIGIN_ALLOW_ALL = True
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES':['rest_framework.permissions.AllowAny'],
    'DEFAULT_PARSER_CLASSES': ['rest_framework.parsers.JSONParser'],
}
```

Let’s run the server.

```
$ python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...
System check identified no issues (0 silenced).
November 08, 2019 - 07:24:30
Django version 2.2.7, using settings 'core.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

## Section 2. Define The NewsContent Model And Admin

Create **NewsContent** Model in `core/newscontents/models.py`.

```python
from django.conf import settings
from django.contrib.auth import get_user_model
from django.db import models
USER_MODEL = get_user_model()
class NewsContent(models.Model):
    reporter = models.ForeignKey(
        to=USER_MODEL,
        on_delete=models.CASCADE,
        related_name='news_contents',
    )
    headline = models.CharField(max_length=255)
    body = models.TextField()
    def __str__(self):
        return self.headline
```

Run migrations.

```
$ python manage.py makemigrations
$ python manage.py migrate
```

The admin site of **NewsContentModel** in `core/newscontents/admin.py`.

```
from django.contrib import admin
from .models import NewsContent

admin.site.register(NewsContent)
```

Create a supuruser.

```
$ python manage.py createsuperuser
Username: reporter
Email address: reporter@example.com
Password: 
Password (again):
Superuser created successfully.
```

Let’s start the server again.

```
$ python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...
System check identified no issues (0 silenced).
November 08, 2019 - 07:35:23
Django version 2.2.7, using settings 'core.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

## Section 3. Configure News Content API

Add some urls so we can access the news content API in `core/urls.py`.

```python
....
from django.urls import include, path
urlpatterns = [
    ....
    path('api/', include('newscontents.urls')),
]
```

Then, create a serializer for **NewsContent** model in `newscontents/serializers.py`.

```python
from rest_framework import serializers
from .models import NewsContent
class NewsContentSerializer(serializers.ModelSerializer):
    class Meta:
        fields = ('id', 'reporter', 'headline', 'body')
        model = NewsContent
```

Next we’ll create the view in `newscontents/views.py`.

```python
from rest_framework import viewsets
from .models import NewsContent
from .serializers import NewsContentSerializer
class NewsContentViewSet(viewsets.ModelViewSet):
    queryset = NewsContent.objects.all()
    serializer_class = NewsContentSerializer
```

Create the urls in `newscontents/urls.py`.

```python
from django.urls import path
from rest_framework.routers import SimpleRouter
from .views import NewsContentViewSet
router = SimpleRouter()
router.register('news-content', NewsContentViewSet, base_name="news_content")
urlpatterns = router.urls
```

## Section 4. Authentication And Permissions

Firstly, create a new user in the admin panel. Then, add the following line in `core/urls.py`.

```python
urlpatterns = [
    ....
    path('auth/', include('rest_framework.urls')),
]
```

Then, run to server and we will create a new user and enter news content from admin panel.

```
$ python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...
System check identified no issues (0 silenced).
November 08, 2019 - 11:08:55
Django version 2.2.7, using settings 'core.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

You can verify this by logging on and then going to `http://localhost:8000/api/news-content/`.

We have configured rest_framework with **AllowAny**. So, each users have access to newscontents. Now, we need to change into your `core/settings.py` file:

```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': ["rest_framework.permissions.IsAuthenticated",],
    'DEFAULT_PARSER_CLASSES': ['rest_framework.parsers.JSONParser'],
}
```

We need to change in `newscontents/views.py` for ensure a user sees only own **News Contents** objects and set user as reporter of a **News Contents** object.

```python
from rest_framework import permissions, viewsets
from rest_framework.exceptions import PermissionDenied
from .models import NewsContent
from .serializers import NewsContentSerializer
class IsReporter(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.reporter == request.user
class NewsContentViewSet(viewsets.ModelViewSet):
    serializer_class = NewsContentSerializer
    permission_classes = (IsReporter,)
    # Ensure a user sees only own News Content objects.
    def get_queryset(self):
        user = self.request.user
        if user.is_authenticated:
            return NewsContent.objects.filter(reporter=user)
        raise PermissionDenied()
    # Set user as owner of a NewsContents object.
    def perform_create(self, serializer):
        serializer.save(owner=self.request.user)
```

If you visit `http://localhost:8000/api/news-content/`, you should only see the news contents belonging to the current logged in user. So, we can remove the reporter field from the serializer in `newscontents/serializers.py`:

```python
from rest_framework import serializers
from .models import NewsContent
class NewsContentSerializer(serializers.ModelSerializer):
    class Meta:
        fields = ('id', 'headline', 'body')
        model = NewsContent
```

We have configure for authentication and permissions. But DRF using still session authentication. Now we will use Json token authentication(JWT) .

## Section 5. Adding JWT(JSON Web Token)
We will use a library (*djangorestframework_simplejwt*).

```
$ pip install djangorestframework_simplejwt
```

We add it to `DEFAULT_AUTHENTICATION_CLASSES` in `core/settings.py`.

```python
REST_FRAMEWORK = {
 'DEFAULT_PERMISSION_CLASSES':["rest_framework.permissions.IsAuthenticated"],
    'DEFAULT_PARSER_CLASSES': ['rest_framework.parsers.JSONParser'],
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework_simplejwt.authentication.JWTAuthentication',
       ],
}
```

Add new end points for **token** of api and **refresh** of token in `core/urls.py`.

```python
from rest_framework_simplejwt.views import TokenObtainView, TokenRefreshView
urlpatterns = [
    ....
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain'),
    # get a new token before the old expires.
    path('api/token/refresh/', TokenRefreshView.as_view, name='token_refresh'),
]
```

### User SIGN-UP

We need new endpoint for users can sign up. Now create a new app for **jwtauth**.

```
$ python manage.py startapp jwtauth
```

Add app in core/settings.py.

```python
INSTALLED_APPS = [
    ....
    'jwtauth',
]
```

Then, we’ll create a new serializer for the **User** model. Add `jwtauth/serializers.py`.

```python
from django.contrib.auth import get_user_model
from rest_framework import serializers
User = get_user_model()
class UserCreateSerializer(serializers.ModelSerializer):
    password = serializers.CharField(
        write_only=True, required=True, style={'input_type': 'password'}
    )
    password2 = serializers.CharField(
        style={'input_type': 'password'}, write_only=True, label='Confirm password'
    )
    class Meta:
        model = User
        fields = ['username', 'email', 'password', 'password2']
        extra_kwargs = {'password': {'write_only': True}}
    def create(self, validated_data):
        username = validated_data['username']
        email = validated_data['email']
        password = validated_data['password']
        password2 = validated_data['password2']
        if (email and User.objects.filter(email=email).exclude(username=username).exists()
        ):
            raise serializers.ValidationError(
                {'email': 'Email addresses must be unique.'}
            )
        if password != password2:
            raise serializers.ValidationError({'password': 'The two passwords differ.'})
        user = User(username=username, email=email)
        user.set_password(password)
        user.save()
        return user
```

Then we’ll create a new view in `jwtauth/views.py`.

```python
from django.contrib.auth import get_user_model
from rest_framework import permissions
from rest_framework import response, decorators, permissions, status
from rest_framework_simplejwt.tokens import RefreshToken
from .serializers import UserCreateSerializer
User = get_user_model()
@decorators.api_view(['POST'])
@decorators.permission_classes([permissions.AllowAny])
def registration(request):
    serializer = UserCreateSerializer(data=request.data)
    if not serializer.is_valid():
        return response.Response(serializer.errors, status.HTTP_400_BAD_REQUEST)        
    user = serializer.save()
    refresh = RefreshToken.for_user(user)
    res = {
        'refresh': str(refresh),
        'access': str(refresh.access_token),
    }
    return response.Response(res, status.HTTP_201_CREATED
```

We add the view to `jwtauth/urls.py`.

```python
from django.urls import path
from .views import registration
urlpatterns = [
    path('register/', registration, name='register')
]
```

Next, include the **jwtauth** urls in `core/urls.py`.

```python
urlpatterns = [
    ....
    path('api/jwtauth/', include('jwtauth.urls'), name='jwtauth'),
]
```

## Section 6. Swagger Docs

```
$ pip install django-rest-swagger
```

Add it to your **INSTALLED_APPS** list in `core/settings.py`.

```python
INSTALLED_APPS = [
    'rest_framework_swagger',
    ....
]
REST_FRAMEWORK = {
    ....
 'DEFAULT_SCHEMA_CLASS':'rest_framework.schemas.coreapi.AutoSchema',
}
```

Add following code `core/urls.py`.

```python
from django.contrib import admin
from django.urls import path, include
from rest_framework_swagger.views import get_swagger_view
schema_view = get_swagger_view(title="News Contents API")
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('newscontents.urls')),
    path('auth/', include('rest_framework.urls')),
    path('api/jwtauth/', include('jwtauth.urls'), name='jwtauth'),
    path('api/docs/', schema_view),
]
```

Then put the token and refresh enpoints in `jwtauth/urls.py`.

```python
from django.urls import path
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView
from .views import registration
urlpatterns = [
    path("register/", registration, name="register"),
    path("token/", TokenObtainPairView.as_view(), name="token_obtain_pair"),
    path("refresh/", TokenRefreshView.as_view(), name="token_refresh"),
]
```

You can visit to `http://localhost:8000/api/docs/` for the full list of APIs and the token endpoints now belong to the **/jwtauth** grouping.

# References

- https://www.django-rest-framework.org
- https://jwt.io/introduction/
- https://github.com/adamchainz/django-cors-headers
- https://github.com/davesque/django-rest-framework-simplejwt
- https://django-rest-swagger.readthedocs.io/en/latest/