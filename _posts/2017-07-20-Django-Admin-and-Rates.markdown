---
layout: post
title:  "Adding Rates and Admin Tweaking in Django"
date:   2017-07-20 09:56:00 -0700
categories: Mac Python Django Application
---

I have completed more or less the skeleton of functionality for the rating application. Now it is time to start adding more parameters and rates so that I can get a more realistic sense of how it will be rated. I have no idea what the company is using for rates, but will start with something relatively simple.

In the last exercise, I was able to create a services.py and get the application to insert it as a layer of logic between the model and the view layer. This way, I can isolate the combining of the two objects and perform basic mathematical functions against the two. For example, if I configure a rate for Number Of Plants to be $15.00 er plant, then the number of plants that the user enters will be multiplied by $15.00 to give me the premium quote.

Putting it all together, I ended up with the following...

I added the additional parameters to the Quote model in models.py:

{% highlight python %}
    percent_recreational = models.PositiveSmallIntegerField(default=0)
    separate_records = models.BooleanField(default=False)
    maximum_plants = models.PositiveSmallIntegerField(default=0)
    testing_lab = models.BooleanField(default=False)
    stream_of_commerce = models.BooleanField(default=False)

    rate = models.DecimalField(max_digits=10, decimal_places=2)
{% endhighlight %}

I then updated services.py to look-up each parameter and return the rate value for the given parameter. It then adds each of those quote parameters together to combine a single Quote. 

I kept this separated for now, as I don't know how the rates are calculated. As I learn more about the quoting process, I'll be able to adjust each individually without having to recode the entire method if I were to make it more dynamic and parameter driven. 

{% highlight python %}
def getFarmQuote(quote_id):
    quote = Quote.objects.get(pk=quote_id)

    growRate = getRate('grow_operations', quote.grow_operations)

    recRate = 0
    if quote.percent_recreational > 0:
        recRate = getRate('percent_recreational', 'percent')
        recRate = quote.percent_recreational * recRate

    sepRate = getRate('separate_records', quote.separate_records)

    plantRate = 0
    if quote.maximum_plants > 0:
        plantRate = getRate('maximum_plants', 'plant')
        plantRate = quote.maximum_plants * plantRate

    labRate = 0
    if quote.testing_lab == False:
        labRate = getRate('testing_lab', quote.testing_lab)

    commerceRate = 0
    if quote.stream_of_commerce == True:
        commerceRate = getRate('stream_of_commerce', quote.stream_of_commerce)

    quote.rate = growRate + recRate + sepRate + plantRate + labRate + commerceRate

    quote.save()
    return quote
{% endhighlight %}

Finally, I updated the template to display all of the parameters of the quote in quote_detail.html (by the way, I renamed the quoteDetail to quote_detail. I think my java days of the lowerUpper naming convention were kicking in, but to stick with the Django tutorial style, I decided to use the first_second naming convention).

{% highlight html %}
<table>
  <tr><td>Business Operation Type:</td><td>{{ quote.business_type }}</td></tr>
  <tr><td>Grow Operations:</td><td>{{ quote.grow_operations }}</td></tr>
  <tr><td>Percent Recreational:</td><td>{{ quote.percent_recreational }}%</td></tr>
  <tr><td>Separate Records:</td><td>{{ quote.separate_records }}</td></tr>
  <tr><td>Maximum Plants:</td><td>{{ quote.maximum_plants }}</td></tr>
  <tr><td>Use Testing Lab:</td><td>{{ quote.testing_lab }}</td></tr>
  <tr><td>Stream of Commerce:</td><td>{{ quote.stream_of_commerce }}</td></tr>
  <tr><td>Quote:</td><td>{{ quote.rate }}</td></tr>
</table>
{% endhighlight %}

I didn't need to make any adjustments to urls.py or views.py (other than the name change to quote_detail), as I have already configured those to work end-to-end.

I added the additional parameters to the FarmRate using the Admin interface:

![Farm Rate Admin Parameters](/assets/admin_params.png){:class="img-responsive"}{:height="400x"}

Now, I should see the entire quote from the front-end after the services.py calcuations have been applied to the quote:

![Updated Farm Quote](/assets/updated_farm_quote.png){:class="img-responsive"}{:height="300x"}

I'll pretty everything up after I complete more features of the application, but for now, plain is just fine for me!

One of the other activities I completed in this round was to modify the FarmRate view in the admin screen so that I could see all of the parameters and their values in a list, rather than only one column. This was a relatively simple add by doing the following in admin.py:

{% highlight python %}
from django.contrib import admin
from .models import Quote, FarmRate

class FarmRateAdmin(admin.ModelAdmin):
    list_display = ('quote_parameter', 'parameter_name', 'parameter_value') 

admin.site.register(Quote)
admin.site.register(FarmRate, FarmRateAdmin)
{% endhighlight %}

This is why the above screenshot is in a nice list display instead of just the single returned value.

Next, I need to start putting the QuoteForm in place. Once that is in place and is returning the quote from the user's input variables, that should be a basic, fully featured application. After that, it's just a matter of adding features, making it user friendly, and, of course, making it pretty.

On to the next exercise!