# Message-Board-App
A basic Message Board application where users can post and read short messages

##  Tutorial
- Create a directory for your project, in our case "message_board_app"
- cd into that directory and create a virtual environment, then install django within the virtual environment
- create a project using the `django-admin startproject <project name>` command
- Perform the migrations `python manage.py migrate`
- Then create an app Posts, using the `python manage.py startapp Posts`

### Configurations
- We need to handle some configurations for our project
- After creating a new app, we need to configure it in the main project folder,
by adding it to installed apps section in the settings.py file

### Model
This app is going to be using the database, so we need to create a model
in the apps model.py file

```python
from django.db import models
# Create your models here.
class Post(models.Model):
    text = models.TextField()

    def __str__(self):
        return self.text[:5] + ' ...'
```
### View
- We also need to create a view for our home page
- We will create it in the views.py file of the app
- The view is going to help as display the content of the model
using the template, so we need to import our model from the model.py file
```python
from .models import Post
from django.views.generic import ListView

# Create your views here.
class HomePageView(ListView):
    model = Post
    template_name = "home.html"
```
- As you can see, we also sepcify the template to use to render our view

### Template
- We need to create the template for rendering our view
- We will create the template folder inside the main project folder
- Then in the settings.py file, we will add the follwing line in the templates section  `'DIRS': [BASE_DIR / "templates"],`
- This basically tells django where to locate the templates

### URLs
- Once you have created your view, we also need to configure its route/url
- In our ulrs.py file in the main project folder, in the urlpatterns list,
we will add another path to our view
`path('', include('posts.urls'))`
- Once this is done, we also need to configure a url route in our app folder
- We will create a urls.py file in the app folder, then add the configuration for the url
```python
from django.urls import path
from .views import HomePageView

urlpatterns = [
    path("", HomePageView.as_view(), name="home"),
]
```
### Configure Admin
- We also need to configure the admin app, to allow it to see our model
- In our admin.py file, we will register our Posts model like follows
```python
from django.contrib import admin
from .models import Post

# Register your models here.
admin.site.register(Post)
```
- That's most of the work
- We just need to apply migrations, and configure our tests
- Every time we make changes to our models file, we need to make migrations by running the following command `python manage.py makemigrations <app name>`
where in our case, the app name is posts
- Finally we can apply migrations using the command `python manage.py migrate`

### Tests
- One of the most crucial aspects of any programming project, is testing
- Testing allows us to see how our programms handle accros different situations
This is the test suite for our message board app
```python
from django.test import TestCase
from django.urls import reverse
from .models import Post

# Create your tests here.
class PostTests(TestCase):
    @classmethod
    def setUpTestData(cls):
        cls.post = Post.objects.create(text="This is a test!")
    
    def test_model_content(self):
        self.assertEqual(self.post.text, "This is a test!")
    
    def test_url_exists_at_correct_location(self):
        response = self.client.get("/")
        self.assertEqual(response.status_code, 200)
    
    def test_homepage(self):
        response = self.client.get(reverse("home"))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "home.html")
        self.assertContains(response, "This is a test!")
        
```
That's it for our message board app.
- Stay tune dfor tutorial on how to host