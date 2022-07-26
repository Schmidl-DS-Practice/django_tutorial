# Customize the admin form
```
from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'question_text']

admin.site.register(Question, QuestionAdmin)
```

## Note:
- You’ll follow this pattern – create a model admin class, then pass it as the second argument to admin.site.register() – any time you need to change the admin options for a model.

```
from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]

admin.site.register(Question, QuestionAdmin)
```

- The first element of each tuple in fieldsets is the title of the fieldset. Here’s what our form looks like now:

# Adding related objects
```
from django.contrib import admin

from .models import Choice, Question
# ...
admin.site.register(Choice)
```

Now “Choices” is an available option in the Django admin.

Also note the “Add Another” link next to “Question.” Every object with a ForeignKey relationship to another gets this for free. When you click “Add Another”, you’ll get a popup window with the “Add question” form. If you add a question in that window and click “Save”, Django will save the question to the database and dynamically add it as the selected choice on the “Add choice” form you’re looking at.

But, really, this is an inefficient way of adding Choice objects to the system. It’d be better if you could add a bunch of Choices directly when you create the Question object. Let’s make that happen.

Remove the register() call for the Choice model. Then, edit the Question registration code to read:
```
from django.contrib import admin

from .models import Choice, Question


class ChoiceInline(admin.StackedInline):
    model = Choice
    extra = 3


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]

admin.site.register(Question, QuestionAdmin)
```

This tells Django: “Choice objects are edited on the Question admin page. By default, provide enough fields for 3 choices.”

It works like this: There are three slots for related Choices – as specified by extra – and each time you come back to the “Change” page for an already-created object, you get another three extra slots.


One small problem, though. It takes a lot of screen space to display all the fields for entering related Choice objects. For that reason, Django offers a tabular way of displaying inline related objects. To use it, change the ChoiceInline declaration to read:
```
class ChoiceInline(admin.TabularInline):
    #...
```
With that TabularInline (instead of StackedInline), the related objects are displayed in a more compact, table-based format

# Customize the admin change list
```
class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

models.py:
```
from django.contrib import admin

class Question(models.Model):
    # ...
    @admin.display(
        boolean=True,
        ordering='pub_date',
        description='Published recently?',
    )
    def was_published_recently(self):
        now = timezone.now()
        return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

```
Edit your polls/admin.py file again and add an improvement to the Question change list page: filters using the list_filter. Add the following line to QuestionAdmin
```

`list_filter = ['pub_date']`

`search_fields = ['question_text']`

## Note:
- That adds a search box at the top of the change list. When somebody enters search terms, Django will search the question_text field. You can use as many fields as you’d like – although because it uses a LIKE query behind the scenes, limiting the number of search fields to a reasonable number will make it easier for your database to do the search.

# Customize the admin look and feel
# Customizing your project’s templates
Create a templates directory in your project directory (the one that contains manage.py). Templates can live anywhere on your filesystem that Django can access. (Django runs as whatever user your server runs.) However, keeping your templates within the project is a good convention to follow.

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

DIRS is a list of filesystem directories to check when loading Django templates; it’s a search path.

Now create a directory called admin inside templates, and copy the template admin/base_site.html from within the default Django admin template directory in the source code of Django itself (django/contrib/admin/templates) into that directory.

If you have difficulty finding where the Django source files are located on your system, run the following command:
` python -c "import django; print(django.__path__)"`


Then, edit the file and replace {{ site_header|default:_('Django administration') }} (including the curly braces) with your own site’s name as you see fit. You should end up with a section of code like:

```
{% block branding %}
<h1 id="site-name"><a href="{% url 'admin:index' %}">Polls Administration</a></h1>
{% endblock %}
```

## Note:
Note that any of Django’s default admin templates can be overridden. To override a template, do the same thing you did with base_site.html – copy it from the default directory into your custom directory, and make changes.


