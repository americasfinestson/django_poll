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

## Reference
The following resources were used in creating this README:
* https://www.zappycode.com/tutorials/keep-secrets-secret-how-to-hide-sensitive-data-in-django
* https://docs.djangoproject.com/en/4.1/intro/
