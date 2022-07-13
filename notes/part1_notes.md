# Create conda environment
`conda create -n *environment name* python`
`conda activate *environment name*`

# Quick install Django
`python -m pip install Django`
`python -m django --version`

# Create Project:
`django-admin startproject *project name*`

## Note:
- You’ll need to avoid naming projects after built-in Python or Django components. In particular, this means you should avoid using names like django (which will conflict with Django itself) or test (which conflicts with a built-in Python package).

## Where should this code live?

- If your background is in plain old PHP (with no use of modern frameworks), you’re probably used to putting code under the web server’s document root (in a place such as /var/www). With Django, you don’t do that. It’s not a good idea to put any of this Python code within your web server’s document root, because it risks the possibility that people may be able to view your code over the web. That’s not good for security.

- Put your code in some directory outside of the document root, such as /home/mycode.

# Starting Project Files
- The outer mysite/ root directory is a container for your project. Its name doesn’t matter to Django; you can rename it to anything you like.
- manage.py: A command-line utility that lets you interact with this Django project in various ways. You can read all the details about manage.py in django-admin and manage.py.
- The inner mysite/ directory is the actual Python package for your project. Its name is the Python package name you’ll need to use to import anything inside it (e.g. mysite.urls).
- mysite/__init__.py: An empty file that tells Python that this directory should be considered a Python package. If you’re a Python beginner, read more about packages in the official Python docs.
- mysite/settings.py: Settings/configuration for this Django project. Django settings will tell you all about how settings work.
- mysite/urls.py: The URL declarations for this Django project; a “table of contents” of your Django-powered site. You can read more about URLs in URL dispatcher.
- mysite/asgi.py: An entry-point for ASGI-compatible web servers to serve your project. See How to deploy with ASGI for more details.
- mysite/wsgi.py: An entry-point for WSGI-compatible web servers to serve your project. See How to deploy with WSGI for more details.

# The Development Server
`python manage.py runserver`

## Note
- Don’t use this server in anything resembling a production environment. It’s intended only for use while developing. (We’re in the business of making web frameworks, not web servers.)

# Changing the port
`python manage.py runserver 8080`

# Change port and server's IP
`python manage.py runserver 0:8000`

## Note:
- 0 is a shortcut for 0.0.0.0. Full docs for the development server can be found in the runserver reference.

# To create app
`python manage.py startapp *app name*`

# Write your first view
``from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")``

## Note:
- To call the view, we need to map it to a URL - and for this we need a URLconf.

- To create a URLconf in the polls directory, create a file called urls.py.


# In polls/urls.py
``from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]``

# In mysite/urls.py
The next step is to point the root URLconf at the polls.urls module. Add an import for django.urls.include and insert an include() in the urlpatterns list, so you have:

## Note:
- see mysite/urls.py for code implementation

- The `include()` function allows referencing other URLconfs. Whenever Django encounters `include()`, it chops off whatever part of the URL matched up to that point and sends the remaining string to the included URLconf for further processing.
- The idea behind `include()` is to make it easy to plug-and-play URLs. Since polls are in their own URLconf (polls/urls.py), they can be placed under `“/polls/”`, or under `“/fun_polls/”`, or under `“/content/polls/”`, or any other path root, and the app will still work.
- You should always use `include()` when you include other URL patterns. `admin.site.urls` is the only exception to this.

# To verify index view is wired into the URLconf
`python manager.py runserver`

## Note:
- Go to http://localhost:8000/polls/ in your browser, and you should see the text “Hello, world. You’re at the polls index.”, which you defined in the index view.
- If you go to http://localhost:8000/, a 404 error will be displayed

# Final Notes
- The `path()` function is passed four arguments, two required: route and view, and two optional: kwargs, and name. At this point, it’s worth reviewing what these arguments are for.
- `path()` argument: `route`
route is a string that contains a URL pattern. When processing a request, Django starts at the first pattern in urlpatterns and makes its way down the list, comparing the requested URL against each pattern until it finds one that matches.

Patterns don’t search GET and POST parameters, or the domain name. For example, in a request to https://www.example.com/myapp/, the URLconf will look for myapp/. In a request to https://www.example.com/myapp/?page=3, the URLconf will also look for myapp/.

- `path()` argument: `view¶`
When Django finds a matching pattern, it calls the specified view function with an HttpRequest object as the first argument and any “captured” values from the route as keyword arguments. We’ll give an example of this in a bit.

- `path()` argument: `kwargs¶`
Arbitrary keyword arguments can be passed in a dictionary to the target view. We aren’t going to use this feature of Django in the tutorial.

- `path()` argument: `name¶`
Naming your URL lets you refer to it unambiguously from elsewhere in Django, especially from within templates. This powerful feature allows you to make global changes to the URL patterns of your project while only touching a single file.