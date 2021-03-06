---
layout: post
title:  "Building Out the First Application in Django"
date:   2017-07-18 12:31:00 -0700
categories: development
---
Now that the basic application has been setup, it is time to start building out the application. In hindsight, I should have named the application so that it was specific to a certain type, for example, farmrateapp, for rating farms. But, I will build this first one as a generic template that I can copy into the other for easier integration.

I pulled the application from the CannaSure website. It is a standard application that has all business types contained within the same application. Here, we want to focus on just the elements that are specific to rating farms. I believe these will be the number of plants, crop yield, crop value, equipment and number of employees. 

Here, we will start with the first two: Business Type (Farm) and Grow Operations Type (indoor, outdoor, or greenhouse). To add these two elements, we have to first build out the model. In this case, our model is the Quote (later to be renamed to FarmQuote). In it, I will include two fields; BusinessType and GrowOperation.

From rateapp/models.py, I will add the following object (class):

{% highlight shell %}
from django.db import models

class Quote(models.Model):
    business_type = models.CharField(max_length=20)
    grow_operations = models.CharField(max_length=10)
{% endhighlight %}

I need to add the rateapp to the master site as follows in CannaInsureRate/settings.py:

{% highlight shell %}
INSTALLED_APPS = [
    'rateapp.apps.RateappConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
{% endhighlight %}

I verified that the rateapp is present in the rateapp/apps.py file.

Next, I need to build the initial objects for creating the objects in the database:

{% highlight shell %}
$ python manage.py makemigrations rateapp

Migrations for 'rateapp':
  rateapp/migrations/0001_initial.py
    - Create model Quote

{% endhighlight %}

Finally, I need to build the objects in the database. Again, at this point, I am using sqlite, the default database in Django, but will be switching to PostreSQL once the framework has been laid.

{% highlight shell %}
$ python manage.py migrate

Operations to perform:
  Apply all migrations: admin, auth, contenttypes, rateapp, sessions
Running migrations:
  Applying rateapp.0001_initial... OK

{% endhighlight %}

At this point, the documentation goes through how a developer can use the command line to build out and communicate with models, but I have made this simple enough that everything should work ok.

There is an issue with what I want the object to return when queried. Ultimately, I would like each record to return a unique ID which includes the person's name, date, and quote number. I believe each object has a hidden unique ID that is automatically generated by Django. Let me research that and see if I can add that to the object.

After looking at the SQL, I can add the following to the object in models.py:

//can't use ID here. Probably a type casting issue. Switching to business type for now.

{% highlight shell %}
def __str__(self):
  return self.id
{% endhighlight %}

Instead of using the command line test everything, I think I want to start with the Admin module. To enable the model so that we can see it in the admin module, we need to add the following to admin.py:

{% highlight shell %}
from .models import Quote

admin.site.register(Quote)
{% endhighlight %}

With this, I can see the Quote in the admin module, but if I try to save it, it will error. To fix this issue, I needed to change the return type from ID to the business type (for now).

Next, I want to restrict the choices for each of these parameters to include only those that I am currently accepting by adding choices to each of the fields, which Django will render as pulldonwn menus:

{% highlight shell %}
    FARM = 'Farm'
    BUSINESS_TYPE_CHOICES = (
        (FARM, 'Farm'),
    )
    business_type = models.CharField(
        max_length=20,
        choices=BUSINESS_TYPE_CHOICES,
        default=FARM
    )

    OUTDOOR = 'Outdoor'
    INDOOR = 'Indoor'
    GREENHOUSE = 'Greenhouse'

    OPERATION_TYPE_CHOICES = (
        (OUTDOOR, 'Outdoor'),
        (INDOOR, 'Indoor'),
        (GREENHOUSE, 'Greenhouse'),
    )
    grow_operations = models.CharField(
        max_length=10,
        choices=OPERATION_TYPE_CHOICES
    )
{% endhighlight %}

Next, I need to render this Model to the front-end, i.e. outside of the Admin module. To do that, I'll start with a page that will list out all of the quotes that have been submitted to date. This is essentially just a list of the quotes in the database without a filter.

This is relatively straightforard. Modify rateapp/views.py:

{% highlight shell %}
from django.shortcuts import render
from django.http import HttpResponse
from .models import Quote

def index(request):
    quotes_list = Quote.objects.all()
    context = {'quotes_list': quotes_list}
    return render(request, 'rateapp/index.html', context)
{% endhighlight %}

Then, create rateapp/templates/rateapp/index.html

Then, within that new template, add the following:

{% highlight shell %}
{% raw %}
{% if quotes_list %}
    <ul>
    {% for quote in quotes_list %}
        <li><a href="/rateapp/{{ quote.id }}/">{{ quote.business_type }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No quotes are available.</p>
{% endif %}
{% endraw %}
{% endhighlight %}

Rebooting the server (the application didn't auto-refresh to include the Quote list), I can now see the single record I entered in the Admin section.

![Quote List](/assets/quotes_list.png){:class="img-responsive"}{:height="100px"}

On clicking the first (and only) Quote, however, Django will return an error, because I have not given instruction for that link yet. I will add a 404 Not Found logic as follows:

In views.py: 
{% highlight shell %}
from django.shortcuts import get_object_or_404, render

....

def quoteDetail(request, quote_id):
    quote = get_object_or_404(Quote, pk=quote_id)
    return render(request, 'rateapp/quoteDetail.html', {'quote': quote})

{% endhighlight %}

Now, reloading the page will return a 404 Not Found error, which will be replaced with an actual page in the future.

Once that is taken care of, I need to now load the detail for a Quote request. In other words, from the quote list, I should be able to select a Quote and view the details of that particular quote. I am not going to add an actual quote to this yet, as I haven't built that logic, but it should show me the two parameters that I have added to the model.

I've already defined the view (above), so now is just a matter of creating a template and setting the correct URL.

I created the template by adding a new file in rateapp/templates/rateapp/quoteDetail.html

I then added some basic information to that file:


{% highlight python %}
{% raw %}
<h1>{{ quote.business_type }}</h1>
<h1>{{ quote.grow_operations }}</h1>
{% endraw %}
{% endhighlight %}

I then added a URL pattern for the new quoteDetail request:

{% highlight python %}
    url(r'^(?P<quote_id>[0-9]+)/$', views.quoteDetail, name='quoteDetail'),
{% endhighlight %}

This will provide a list of quotes and the quote details to anyone that requests it. In the future, however, I want to make this an admin feature and only allow quotes to be viewed by the user that entered them. For that, I will have to change the query to render the quote based on the username, but I'll get to that later. For now, this should be it to render the quoteDetail response.

![Quote Detail](/assets/quote_detail.png){:class="img-responsive"}{:height="150px"}

So far, so good. We now have our first object (Quote) capable of being entered in the Admin, then a list of Quotes dispolayed through the application, and the details for each quote avaiable on clicking the quote in the quote list. Overall, a pretty good start.

Next, I will work on building a FarmRate object that will be used by the Admin to list rate variables. This will be combined with a FarmRate 'controller', which will then be used to start the calculations and ultimately return the QuoteResponse.

I'll start that in the next post.