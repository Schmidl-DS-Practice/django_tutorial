# Customize your app’s look and feel
First, create a directory called static in your polls directory. Django will look for static files there, similarly to how Django finds templates inside polls/templates/.

Django’s STATICFILES_FINDERS setting contains a list of finders that know how to discover static files from various sources. One of the defaults is AppDirectoriesFinder which looks for a “static” subdirectory in each of the INSTALLED_APPS, like the one in polls we just created. The admin site uses the same directory structure for its static files.

Within the static directory you have just created, create another directory called polls and within that create a file called style.css. In other words, your stylesheet should be at polls/static/polls/style.css. Because of how the AppDirectoriesFinder staticfile finder works, you can refer to this static file in Django as polls/style.css, similar to how you reference the path for templates.

