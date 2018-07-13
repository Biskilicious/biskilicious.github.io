---
layout: post
title:  "Adding Authorization to Forms and Views in Django"
date:   2017-07-20 13:23:00 -0700
categories: development
---
Now that the application is working end-to-end for the end-user, I want to start restricting what certain groups can and can't see. Namely, I want the application to display the entire list of quotes to a solicitor (or insurer) and restrict the individual quotes to just the user that created them. So, the index should return all quotes if you are a solicitor and only those quotes that you created if you are a customer. 

OK, well, this was rather straight-forward too. All I needed to do was to create a group in the Django admin interface, in this case, I created a group "Customers", then entitled that group for the privilege to create quotes. 

Once that was complete, I updated the views.py to include a basic if/else statement to check whether the user belonged to the Customers group. If they did, then they were permitted to see their quotes (and only their quotes). If they were not a member of Customers, then they could see all quotes, which is something I may clean up later. 

In views.py:

{% highlight python %}
def index(request):
    if request.user.groups.filter(name='Customers').exists():
        quotes_list = Quote.objects.all().filter(requestor = request.user.username)
    else:
        quotes_list = Quote.objects.all()

    context = {'quotes_list': quotes_list}
    return render(request, 'rateapp/index.html', context)
{% endhighlight %}

This test checks whether the group 'Customers' is present in the list of gruops that this user belongs to.

I created a new user, 'customer1' and assigned that user to the Customers group in the Django admin interface. Now, after I log in as Customers, I can see only the quotes that I created under that username. 

![Farm Rate Admin Parameters](/assets/quote_list2.png){:class="img-responsive"}{:height="100x"}

I also added the Requestor (Submitted by) field to the report to make sure it was reporting correctly. 

Next, I wanted to be sure that this list was only available after you logged in, so I added the user_is_authenticate test around the group test. And, I moved this function from the index method to a new method called "get_quotes", which seemed appropriate.

{% highlight python %}
def get_quotes(request):
    logging.basicConfig(stream=sys.stderr, level=logging.DEBUG)
    if request.user.is_authenticated:
        if request.user.groups.filter(name='Customers').exists():
            quotes_list = Quote.objects.all().filter(requestor = request.user.username)
        else:
            quotes_list = Quote.objects.all()
    else:
        return redirect('/accounts/login/')

    context = {'quotes_list': quotes_list}
    return render(request, 'rateapp/index.html', context)
{% endhighlight %}

After a minor change in urls.py to the new view method, I was good to go. If the user is NOT authenticated, it will redirect the user to the login page. If the user IS authenticated, it will perform the test on whether they are a customer or not.

Now that that piece is in place, I wanted a home page that was for the master site, and not any particular application. I created a new project called "home" using:

{% highlight shell %}
$ python manage.py startapp home
{% endhighlight %}

This created the familiar application in my root cannainsurerate application. Now that I think about it, I may change this name to just "CannabisInsurance" and the rateapp to just "quotes". This will make the URL more recognizable to customers and easier to track. But, I will do that in a bit. 

Once the home application was created, I just needed to register the application in settings.py:

{% highlight python %}
INSTALLED_APPS = [
    'home.apps.HomeConfig',
    'customer.apps.CustomerConfig',
    'rateapp.apps.RateappConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
{% endhighlight %}

Then, update the urls.py to point to the new home application.

{% highlight python %}
urlpatterns = [
    ...
    url(r'^', include('home.urls')),
]
{% endhighlight %}

And, within home/urls.py, I added the URL to point to the index file that I wanted to render in my display:

{% highlight python %}
from django.conf.urls import url
from home import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
{% endhighlight %}

In home/views.py, I need logic to redirect the user to an index page, which will include a template that I will create in the next step:

{% highlight python %}
from django.shortcuts import render

def index(request):
    return render(request, 'home/index.html')
{% endhighlight %}

Now, when a user comes to the main site, which, for now, is 127.0.0.1:8001, they will be redirected to 127.0.0.1:8001/home/ and I have a template for processing Django actions, including linking to the other applications (such as creating a quote, registering, logging in, or submitting an application).

I created new directories in home/templates/home and dropped my index.html template into that new directory. In index.html, I added the following:

{% highlight html %}
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN">
<html lang="en">
  <head>
    <title>Cannabis Insurance Services</title>
    <link rel="stylesheet" type="text/css" href="{{ STATIC_URL }}css/style.css" media="screen" />
  </head>
  <body>
    <p>Cannabis Insurance Services</p>
  </body>
</html>
{% endhighlight %}

All this will do is return a template, which will give the static content "Cannabis Insurance Services" to the index. 

![Home Screen](/assets/home_screen.png){:class="img-responsive"}{:height="75px"}

And, Boom goes the dynamite!




[Django Registration Form]:https://www.obeythetestinggoat.com/using-the-built-in-views-and-forms-for-new-user-registration-in-django.html

{% highlight python %}

{% endhighlight %}
