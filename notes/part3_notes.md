# Writing more views
- to polls/views.py:
    -  def detail()
    -  def results():
    -  def vote():

- to polls/urls.py:
    - path('<int:question_id>/', views.detail, name='detail')
    - path('<int:question_id>/results/', views.results, name='results')
    - path('<int:question_id>/vote/', views.vote, name='vote')

# Write views that actually do something
```
from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
```

`First, create a directory called templates in your polls directory. Django will look for templates in there.`

```Your project’s TEMPLATES setting describes how Django will load and render templates. The default settings file configures a DjangoTemplates backend whose APP_DIRS option is set to True. By convention DjangoTemplates looks for a “templates” subdirectory in each of the INSTALLED_APPS.

Within the templates directory you have just created, create another directory called polls, and within that create a file called index.html. In other words, your template should be at polls/templates/polls/index.html. Because of how the app_directories template loader works as described above, you can refer to this template within Django as polls/index.html.```

- in polls/templates/polls/index.html:
```
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}```


## Note
- To make the tutorial shorter, all template examples use incomplete HTML. In your own projects you should use complete HTML documents.

in polls/views.py:
```
from django.template import loader

template = loader.get_template("polls/index.html")
context = {"latest_question_list": latest_question_list}
```

# A Shortcut: render()
- to polls/view.py
    - return render(request, 'polls/index.html', context)

## Note
that once we’ve done this in all these views, we no longer need to import loader and HttpResponse (you’ll want to keep HttpResponse if you still have the stub methods for detail, results, and vote).

# Raising a 404 error
```
from django.http import Http404

def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

# A shortcut: get_object_or_404()
in polls/views.py:
- from django.shortcuts import get_object_or_404
- question = get_object_or_404(Question, pk=question_id)

## Note
There’s also a get_list_or_404() function, which works just as get_object_or_404() – except using filter() instead of get(). It raises Http404 if the list is empty.

in polls/templates/polls/detail.html:
```
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```

# Removing hardcoded URLs in templates
```
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
The problem with this hardcoded, tightly-coupled approach is that it becomes challenging to change URLs on projects with a lot of templates. However, since you defined the name argument in the path() functions in the polls.urls module, you can remove a reliance on specific URL paths defined in your url configurations by using the {% url %} template tag:

<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

# Namespacing URL names
The answer is to add namespaces to your URLconf. In the polls/urls.py file, go ahead and add an app_name to set the application namespace:

to polls/urls.py
`app_name = 'polls'`



