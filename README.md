# django_poll
Basic poll application using Django.

# Setup Instructions

## Clone Git repository

```
$ git clone git@github.com:americasfinestson/django_poll.git
```

## Install required packages

Note that this project was completed on Debian Linux. Install the required packages.
```
$ sudo apt-get install python3-venv
$ sudo apt-get install libpg-dev (if using PostgreSQL)
```

## Create Python virtual environment

Install venv and initialize the Python virtual environment.
```
$ mkdir ~/.venv
$ python3 -m venv ~/.venv/django_poll
$ source ~/.venv/django_poll/bin/activate
```

## Initialize Django project

Install Django and initialize the Django project.
```
$ pip install django
$ django-admin startproject django_poll .
```

## Create Python requirements file
Create the Python requirements file.
```
$ pip freeze > requirements.txt
```

The ```requirements.txt``` file can then be used by others to ensure they the correct Python packages installed for the project.
```
$ pip install -r requirements.txt
```

## Move sensitive data out of VCS

Move the sensitive variables out of ```settings.py```. To do this, start by creating a new file called ```secrets.json```, and add the following content to it.
```
{
    "SECRET_KEY": "<copy/paste from settings.py>"
    "DB_PASSWORD": "<create your database password>"
}
```

Next, modify ```settings.py``` to import this new JSON file. Add the below content where appropriate throughout the file.

```
import json

with open('secrets.json', 'r') as f:
    my_secrets = json.load(f)
    f.close()
    
SECRET_KEY = my_secrets['SECRET_KEY']

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
        'DB_PASSWORD': my_secrets['DB_PASSWORD']
    }
}
```

The old ```SECRET_KEY``` line can (should) now be removed from ```settings.py```.

## Exclude new secrets file from VCS

Exclude ```secrets.json``` from VCS, whether that be via a ```.gitignore``` file, or other means.

# Creating Django Project

## Verify server runs

Run the following command:
```
$ python3 manage.py runserver
```

Open a web browser and navigate to ```http://localhost:8000```. Verify the default Django welcome page appears.

## Create app for polling

The base Django environment/project has been established. Django comes with a built-in utility for creating the basic directory structure for a new application in the project. Run the following command to create an app called ```polls```:

```
$ python3 manage.py startapp polls
```

## Create view for polls app

Django "views" are Python functions that receive HTTP requests from clients and return an HTTP response. A full Django web page uses various views that all serve a distince purpose. Views are placed in ```/path/to/app/views.py```. Open ```polls/views.py``` and add the appropriate content.

## Create URL mapping for new view

In order for the view created in the previous section to be of use, the view must be mapped to a URL. Each app has its own ```/path/to/app/urls.py``` that maps views to URLs. Create ```polls/urls.py``` and add the appropriate content.

## Incorporate ```polls/urls.py``` into overall project URL mapping

The Django project as a whole will not be aware of the ```polls``` application's URLs by default. Open ```django_poll/urls.py``` and add the appropriate content to make the overarching project aware of the URLS of the ```polls``` application.

Now, start the server and access the new view by navigating to ```http://localhost:8000/polls/```.

## Database setup

By default, Django uses SQLite - a lightweight database that is included with Python. SQLite is easy to use and takes little time to get up and running, but is not well-suited for productions. In production, a more scalable database like PostgreSQL should be used. Update ```django_poll/settings.py``` with the appropriate information in the ```DATABASES['default']``` section.

Tables within the database must be created before they can be accessed.

```
$ python3 manage.py migrate
```

The ```migrate``` command looks at the ```INSTALLED_APPS``` setting in ```django_poll/settings.py``` and creates any necessary database tables, as well as any database migrations shipped with any of the apps in the project. A message will appear for each migration that is applied. 

## Create a model for the polling application

In Django, models are essentially the layout of the database. For the polling app, two models will be created:
* ```Question```: The question of the poll, and its publication date
* ```Choice```: The text of the choice and a vote tally. Each ```Choice``` is associated with a ```Question```.

Edit ```polls/models.py``` and add a class for both ```Question``` and ```Choice```. Both classes have a number of class variables, which each represent a database field in the model.

## Include ```polls``` app in project

Now that the databse is configured, include the ```polls``` app in the overaching project. To do this, open ```django_poll/settings.py```, and in the ```INSTALLED_APPS``` list, add ```polls.apps.PollsConfig```.

## Update database

Since changes were made to the models, Django needs to store these changes as a "migration". Migrations are human-readable files that provide a paper trail of changes made to models. These migrations are stored under ```/polls/migrations/```. Run the following command to inform Django that changes have been made to the models, and that the changes need to be saved as a migration.

```
$ python3 manage.py makemigrations polls
```

The above command will create a migration under ```polls/migrations/0001_initial.py```. To see the output of a SQL migration without actually applying the change, run the following command:

```
$ python3 manage.py sqlmigrate polls 0001
```

There are some key things to note here in regards to the out put of the above command:
* The SQL output will vary based on the type of databse being used
* Table names are automatically generated using the naming convention ```app_model``` (e.g. ```polls_question```)
* The ```python3 manage.py check``` command can be used to check your project for any problems without making any migrations or modifying the database in any way.

To apply the migrations, run the following command:

```
$ python3 manage.py migrate
```

Migrations are beneficial because they allow a project's database to be updated over time, without needing to delete the database and/or tables and start fresh. Migrations can be used to update a database live. To summarize, there are three primary steps when making model changes:
* Update ```models.py``` with the required changes
* Run ```python3 manage.py makemigrations``` to create migrations for the changes
* Run ```python3 manage.py migrate``` to apply those migrations

The two migration commands are separated because migrations are designed to be commited to VCS and shipped with the project.

# The Django admin interface

Django ships with an admin interface, making it easy for site admins to manage content, users, groups, and many other admin-related tasks. An admin user can be created by running:

```
$ python3 manage.py createsuperuser
```

By default, the Django admin is accessible at ```http://localhost:8000/admin/```. Apps must be added manually to the admin.

## Add the ```poll``` app to the Django admin

Open ```polls/admin.py``` and add the appropriate content to register with the Django admin.

# Reference
The following resources were used in creating this README:
* https://www.zappycode.com/tutorials/keep-secrets-secret-how-to-hide-sensitive-data-in-django
* https://docs.djangoproject.com/en/4.1/intro/
