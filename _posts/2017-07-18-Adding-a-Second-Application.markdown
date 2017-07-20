---
layout: post
title:  "Adding a Second Application in Django"
date:   2017-07-18 19:51:00 -0700
categories: Mac Python Django Application
---
This is where I think Django offers a lot more features and functionality. I want to add a registration application where users can Register for the site, then submit more information for adding an address and other pertinent information. This should then be usable to create an overall Application. 

I don't want to make this overwhelmingly complicated, so I am going to create a simple user registration, then an Insurance Application applicaiton. There should be a module that allows me to create the registration, so let me read through the Django documentation and find out. BRB...

So, let's start with the registration portion first. 

Following the documentation on the Django website, and another tutorial someone was gracious enough to post, I have added a basic login and logout functionality by doing the following:

In settings.py, I added the following to the TEMPLATES param:

{% highlight shell %}
TEMPLATES = [
    'DIRS': ['./CannaInsureRate/templates',],
{% endhighlight %}

Then, I created those directories under the master site directory. 

I then added a template for each of login.html and logged_out.html, which are two default templates that Django looks for in those directories.

Lastly, I want the login to redirect to the application I just build (rateapp), so I added the following line to the bottom of settings.py:


{% highlight shell %}
LOGIN_REDIRECT_URL = '/rateapp/'
{% endhighlight %}

In my login.html, I added the following basic login information:

{% highlight shell %}
{% raw %}
{% block title %}Login{% endblock %}

{% block content %}
  <h2>Login</h2>
  <form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Login</button>
  </form>
{% endblock %}
{% endraw%}
{% endhighlight %}

And in the master project urls.py, I added the following:

{% highlight shell %}
    url(r'^login/$', auth_views.login, name='login'),
    url(r'^logout/$', auth_views.logout, name='logout'),
{% endhighlight %}

Reloading the server renders the correct login form and redirects to the rateapp index page:

![Login Page](/assets/login_page.png){:class="img-responsive"}{:height="250px"}

And after logging in...

![Login Page](/assets/after_login.png){:class="img-responsive"}{:height="125px"}

I completed everything using the tutorial at this location. It was perfect. It provided working examples, explanations and most importantly, everything worked!

[Django Tutorial][django tutorial]

[django tutorial]: https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Authentication

