---
layout: post
title:  "Adding Business Logic to the First Application"
date:   2017-07-18 16:03:00 -0700
categories: development
---

Here is where the proverbial rubber hits the road. I have a basic object that is saved to the database via the Admin interface (I will create the form later), and it is being displayed from the front-end. Now, I want to include a new object that sets rates according to the parameters and calculate those rates on the fly based on each of the Quote object's parameters that are passed to it. This should return a QuoteResponse that will be the fundametal basis of how this entire application works.

I will repeat the prior steps for building a new FarmRate object as I completed for the Quote. In this object, I will need to define rates for each of the selectable parameters. For example, under grow_operations, there are three choices; outdoor, indoor and greenhouse. Each will have it's own corresponding rate value. The object will then have a quote_parameter field (in this case, grow_operation), a quote_name field and finally, a quote_value. 

I'll add the following to models.py:

{% highlight shell %}
from django.db import models

class FarmRate(models.Model):
    quote_parameter = models.CharField(max_length=20)
    parameter_name = models.CharField(max_length=10)
    parameter_value = models.DecimalField(max_digits=10, decimal_places=2)
{% endhighlight %}

Then enable that new object in my Admin screen (since it is a new model, I need to run a makemigrations command):

{% highlight shell %}
$ python manage.py makemigrations rateapp

Migrations for 'rateapp':
  rateapp/migrations/0002_auto_20170718_2258.py
    - Create model FarmRate
    - Alter field business_type on quote
    - Alter field grow_operations on quote

$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, rateapp, sessions
Running migrations:
  Applying rateapp.0002_auto_20170718_2258... OK
{% endhighlight %}

Add to admin.py:

{% highlight shell %}
from django.contrib import admin
from .models import Quote, FarmRate

admin.site.register(Quote)
admin.site.register(FarmRate)
{% endhighlight %}

Then start the server and load up the Admin page:

![Admin Farm Rate object](/assets/admin_farmrate.png){:class="img-responsive"}{:height="150px"}{:height="300px"}

And then add an actual rate for the grow_operations parameter:

![Admin Farm Rate Details](/assets/admin_farmratedetails.png){:class="img-responsive"}{:height="150px"}{:height="300px"}

Now I have a way to create a rate for each of the parameters in the Quote. These should be able to extend easily as I build the quote object out to include new parameters.

The next exercise is to combine the quote parameters with the actual rates to come up with a base quote. In J2EE this would be helper classes, and I am suspecting the same here, where we will create a Python class that accepts quote as input, queries for the rates, then calculates each field. Let's dive in and see!

OK, so this was easier than I thought. I create a services.py file for capturing the business logic. It's not doing anything yet, just querying the quote and the rates and returning the quote without any actual data crunching. This will come shortly.

{% highlight shell %}
from .models import Quote, FarmRate

def getFarmQuote(quote_id):
    quote = Quote.objects.get(pk=quote_id)
    farmRate = FarmRate.objects.all()

    return quote

{% endhighlight %}

Next, I modified the views.py to get the quote from services.py instead of directly from the model:


{% highlight shell %}
from .services import getFarmQuote

def quoteDetail(request, quote_id):
    quote = getFarmQuote(quote_id)
    return render(request, 'rateapp/quoteDetail.html', {'quote': quote})
{% endhighlight %}

Now, refreshing the browser, I get the quote details returned from the service module instead of directly from the database model. Perfect! Now I just need to match up the rate values with the quote input parameters.

To do this, I modified services.py to include the logic for retrieving a FarmRate record and matching the parameter. Once the rate is retrieved, I can apply the rate to the Quote as follows:

In services.py:
{% highlight shell %}
from .models import Quote, FarmRate

def getFarmQuote(quote_id):
    quote = Quote.objects.get(pk=quote_id)
    quote.rate = getRate('grow_operations', quote.grow_operations)
    return quote

def getRate(param, paramType):

    farmRate = FarmRate.objects.get(
        quote_parameter = param,
        parameter_name = paramType
    )

    return farmRate.parameter_value
{% endhighlight %}

This will take the quote sent from the front-end, query the database for it by the quote_id, retrieve the rate based on the parameters passed from the quote and return the rate.

![Rate Detail](/assets/rate_detail.png){:class="img-responsive"}{:height="150px"}

And I can now see the rate retrieved for Greenhouse...

To test the others, I'll create some records for the other grow_operation types and set different rates for those.

At this point, everything is very simple, but functional. Obviously, this will start to expand out, but for now, I think this is a good starting place.

I think it is best to detour here. Although the rating functionality is very cool, I don't know how I'll be able to build a rating application without the rates. Therefore, I will start working on a second application, which is the application for a new insurance poilcy.

After reviewing the web, it seems that most insurers are using a PDF form that can be downloaded, or you fill out a contact form and submit it. I will accomplish the same, but put everything on-line so that I can capture the information and start the quote process without over-burdening the customer by filling out an entire application.

Let's start a new post for that one!