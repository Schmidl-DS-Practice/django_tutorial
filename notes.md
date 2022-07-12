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

