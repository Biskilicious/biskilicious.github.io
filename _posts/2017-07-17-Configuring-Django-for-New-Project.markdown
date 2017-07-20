---
layout: post
title:  "Configuring Django for a New Project"
date:   2017-07-17 21:03:09 -0700
categories: Mac Python Django Configuration
---
I just completed installing/upgrading Django on this machine. I will now walk through the configuration, setup and first project to get the environment created for the Insurance Rating Application. 

Here, we need to create a new site, which will be the master site for all of the subapplications that I intend to build during this project. Since CannaSure is taken by the parent company, I will name it CannaInsureRate.

{% highlight shell %}
$ django-admin startproject CannaInsureRate
{% endhighlight %}

My Directory now looks like this:

{% highlight shell %}
CannaInsureRate/
  manage.py
  CannaInsureRate/
    wsgi.py
    urls.py
    settings.py
    __init__.py
{% endhighlight %}

Once this is complete, I should be able to test the installation and get Django to return a page:

{% highlight shell %}
$ python manage.py runserver
{% endhighlight %}

And I get the following output:
{% highlight shell %}
Performing system checks...

System check identified no issues (0 silenced).

You have 13 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

July 18, 2017 - 15:15:18
Django version 1.11.3, using settings 'CannaInsureRate.settings'
Starting development server at http://127.0.0.1:8001/
Quit the server with CONTROL-C.
{% endhighlight %}

And opening a browser, I get the familiar "It Worked!" landing page:
![It Worked!](/assets/django_landing_page.png){:class="img-responsive"}

Now that the overall site infrastructure is in place, I can start to work on individual applications to get the application up and runing. I believe the first thing I want to do is add a login feature. I want the login to be either local login (i.e. local accounts or create an account with Google or Facebook, which is becoming standard in the web application world). Let's begin with just the local account creation first. 

I'll need to create a new application for authentication. Luckily, Django already has developed this functionality for authentication and authorization. Information regarding this module can be found here: 

[Django Authentication Documentation][django-authentication]

The first thing I need to do is create a SuperUser. This user will be used to adminster the application from here on out once everything is up and running. But, before I can do that, I need to enable a few features in Django; in particular, the INSTALLED_APPS and MIDDLEWARE in my settings.py file. After checking that both of them exist, Django still will not create the objects associated with them until I have enabled an application that will make use of them. To do this, I need to enable them using Django's startproject command, which I have not yet done. Since, the authentication and authorization pieces are overriding all of the applications, I don't want to create a single application for just authentication and authorization. Instead, I want to start building out my applications. 

The first and most important application to get started is a simple farm rating application for property and general liability. This should accept as few as possible input parameters, process these then return a rate. So, let's call this first one rateapp.

{% highlight shell %}
$ python manage.py startapp rateapp
{% endhighlight %}

This command generated the following on the filesystem:

{% highlight shell %}
CannaInsureRate/
  manage.py
  CannaInsureRate/
    wsgi.py
    urls.py
    settings.py
    __init__.py
  rateapp/
    __init__.py
    admin.py
    apps.py
    migrations/
    models.py
    tests.py
    views.py
{% endhighlight %}

Now that the first application has been built, I want to start fleshing it out, but I am not going to go "big bang" and try to generate everything I think it will need. Instead, I am going to start with a parameter at a time and start incorporating the rules engine from the start.

I guess here would be a good time to explain that I intend to have the controller logic of the MVC framwork to include a callout to a rules engine. This will accept the parameters of the quote request object and perform processing on them by looking up each object that may contribute to the cost of the premium. Once the rules engine completes, the application will return the quote response to the user.

Therefore, the first few actions I will create will be to have a QuoteRequest object, where the user will enter their information, a RatingEngine controller, which will run the calculations, and a QuoteResponse object, which will return the quote to the user. 

Before I start anything, I have a pesky warning message that keeps popping up indicating that I have not created my migrations in the database. So, to remove the warning, I need to run the migrate command:

{% highlight shell %}
$ python manage.py migrate
{% endhighlight %}

Which returns:

{% highlight shell %}
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying sessions.0001_initial... OK
{% endhighlight %}

Now, restarting the server, I have a 'clean' start:
{% highlight shell %}
$ python manage.py runserver 8001

Performing system checks...

System check identified no issues (0 silenced).
July 18, 2017 - 16:40:43
Django version 1.11.3, using settings 'CannaInsureRate.settings'
Starting development server at http://127.0.0.1:8001/
Quit the server with CONTROL-C.

{% endhighlight %}

Now that the first request/response has been created for the rateapp application and have migrated the objects to the database, I can create the superuser:

{% highlight shell %}

$ python manage.py createsuperuser --username=rbdundas --email=rbdundas@gmail.com
Password: 
Password (again): 
Superuser created successfully.

{% endhighlight %}

By default, Django has the admin module already enabled. Creating this SuperUser grants me access to that module. Using my browser, I can now navigate to my local site (http://127.0.0.1:8001/admin/login/?next=/admin/) and bring up the authentication page:

![Django Admin Login](/assets/django_admin_login.png){:class="img-responsive"}{:height="300px"}

Which now gives me access to the Django Admin module after authentication:

![Django Admin Login](/assets/django_admin_module.png){:class="img-responsive"}{:height="250px"}

The admin module doesn't contain any of my new application objects (models), however, the default User and Group objects are present.

Now that everything is setup and I have the application working, albeit in a very basic mode, it is time to start fleshing out the functionality. I think I will create a new post for that.

[django-authentication]:https://docs.djangoproject.com/en/1.11/topics/auth/