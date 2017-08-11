---
layout: post
title:  "Adding a Form to my Quote Application in Django"
date:   2017-07-20 10:29:00 -0700
categories: Mac Python Django Application
---
Now that everything is working from the admin screens, and I have a user view outside of the admin screen, I need a way to capture the user input as a quote and return the quote_detail for that quote (i.e. the actual quote value). To do this, I know that I need a form for entering the quote from the front-end, so will be using Django's form API to build that form.  

Once that is done, I will be furthering the authentication piece by creating a new group called 'customers' and authorize that quote form and quote detail to that group. Further, I want to make the quote_detail viewable only to the user that submitted the quote, so will be working on that today.

First, the form:

The form is relatively straight-forward, but does take some attention to detail to make sure nothing is missed. 

In models.py, I added the ModelForm for Quote:

{% highlight python %}
class QuoteForm(ModelForm):
    class Meta:
        model = Quote
        fields = ['business_type', 'grow_operations', 'percent_recreational', 
        'separate_records', 'maximum_plants', 'testing_lab', 'stream_of_commerce']
{% endhighlight %}

Then updated views.py to include a new method for managing the form post:

{% highlight python %}
def get_quote(request):
    form = QuoteForm()
    if request.method == 'POST':
        form = QuoteForm(request.POST)
        if form.is_valid():
            quote = form.save()
            quote.refresh_from_db()
            return redirect('quote_detail', quote_id=quote.id)
        else:
            form = QuoteForm()
    return render(request, 'rateapp/quote_form.html', {'form': form})
{% endhighlight %}

Next, I needed a HTML template to use for the form. Note the use of the as_table method for the form. Also, it wasn't working until I wrapped the form call in <table> tags.

{% highlight html %}
{% raw %}
<form method="post">
  {% csrf_token %}
  <table>
    {{ form.as_table }}
  </table>
    <input type="submit" value="Submit" />
</form>
{% endraw %}
{% endhighlight %}

Finally, I updated the URL to string it all together in urls.py:

{% highlight python %}
from django.conf.urls import url
from rateapp import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
    url(r'^quote_detail/(?P<quote_id>[0-9]+)/$', views.quote_detail, name='quote_detail'),
    url(r'^quote_request/$', views.get_quote, name='quote_request'),
]
{% endhighlight %}

After refreshing the browser (I don't remember if I required a MakeMigrations for this, but on the safe side, assume it was done), I was able to see the form:

![Quote Request Form](/assets/quote_request_form.png){:class="img-responsive"}{:height="250px"}

At this point, I was having some issues with the linking and the flow. I found that I misnamed quote_detail, I needed to use the refresh_from_db() to get the ID field from the quote and a few other little troubleshooting tasks, but once through those, it worked perfectly.

I then added some URL tags to the templates so that I can move between the indx (the list of all quotes), the quote request form and the quote detail form as a user. 

Now that everything is working end-to-end, I want to set it up so that only authorized users can submit quotes. To do this, I need to work on the authorization piece of the application. Note, I have already completed the authorization piece of the application. Now I just need to add the logic so that 1) only admin groups can see all quotes, and 2) members of the customer group can only see THEIR quotes.

Onto the next post!

