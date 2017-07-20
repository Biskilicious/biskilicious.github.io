---
layout: post
title:  "Installing and Configuring Django"
date:   2017-07-17 18:03:00 -0700
categories: Mac Python Django
---
I have reviewed and tested several web application frameworks over the years. Most recently, I have reviewed and built a few pages using Angular2. I am returning to Django because it is the most straight-forward web application framework. There is more or less a formula to follow when building new applications and I don't have to spend my time troubleshooting a page. Although I may eventually return to the single page application javascript frameworks, I am more than content to stick with what I know and Django is a perfectly suited application framework. 

The documentation here is really a repository for setting up a new project for rating insurance applications. The intent of the documentation is to keep a record of the steps that I have undertaken to create the application and how it was ultimately built and configured.

The instructions for downloading and installing Django are available in the documentation from the djangoprojects website:
[Django Installation Instructions][installation-instructions]

For this project, I will be using PostgreSQL, however, for the initial prototyping, I may just use the sqlite database preconfigured with Django. Here is the link for now, but I will get into the database integration at a later step.
[PostgreSQL Website][postgresql-website]

I should note here that I am using virtualenv and I need to post a write-up explaining how to install and configure virtualenv.

Following the instructions to download and install, I can see the following results in the test:
{% highlight shell %}
(projectsenv) rbdundas:projects rbdundas$ python
Python 3.6.0 (v3.6.0:41df79263a11, Dec 22 2016, 17:23:13) 
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import django
>>> print(django.get_version())
1.10.6
>>> exit()
{% endhighlight %}

I checked the official released version of Django and can see that it is now at version 1.11.0; therefore, I am going to go ahead and update it. I am not planning to update Python at this time, even though it is at 3.6.2.

{% highlight shell %}
(projectsenv) rbdundas:projects rbdundas$ pip update django
ERROR: unknown command "update"
(projectsenv) rbdundas:projects rbdundas$ pippip install -U django
-bash: pippip: command not found
(projectsenv) rbdundas:projects rbdundas$ pip install -U django
Collecting django
  Downloading Django-1.11.3-py2.py3-none-any.whl (6.9MB)
    100% |████████████████████████████████| 7.0MB 211kB/s 
Collecting pytz (from django)
  Downloading pytz-2017.2-py2.py3-none-any.whl (484kB)
    100% |████████████████████████████████| 491kB 2.2MB/s 
Installing collected packages: pytz, django
  Found existing installation: Django 1.10.6
    Uninstalling Django-1.10.6:
      Successfully uninstalled Django-1.10.6
Successfully installed django-1.11.3 pytz-2017.2
(projectsenv) rbdundas:projects rbdundas$ 
{% endhighlight %}

Now, re-running the installation version, I get the following:

{% highlight shell %}
(projectsenv) rbdundas:projects rbdundas$ python
Python 3.6.0 (v3.6.0:41df79263a11, Dec 22 2016, 17:23:13) 
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import django
>>> print(django.get_version())
1.11.3
>>> exit()
{% endhighlight %}

Now that Django has been installed and tested, I will move on to configuration and configuration of the project.

[installation-instructions]:https://docs.djangoproject.com/en/1.11/intro/install/
[postgresql-website]:https://www.postgresql.org/
