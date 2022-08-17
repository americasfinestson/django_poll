# django_poll
Basic poll application, following Django's website tutorial.

# Setup Instructions

## Clone Git repository

```
$ git clone git@github.com:americasfinestson/django_poll.git
```

## Create Python virtual environment

Install venv and initialize the Python virtual environment.
```
$ sudo apt-get install python3-venv
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
