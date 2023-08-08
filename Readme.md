https://www.youtube.com/watch?v=NAOsLaB6Lfc&ab_channel=CodingEntrepreneurs

* I have edited some configurations due to the needs of the time.
* We are using Windows
- Use python 3.8 or later

- Mac
Install  Intel or Universal depends of your requirements

- Windows
Install Windows Installer

- Save python in this directory
C:\Python39\python.exe
 

- Install kubectl in your system
https://kubernetes.io/docs/tasks/tools/

- Install Docker
https://docs.docker.com/engine/install/

- Install doctl
https://github.com/digitalocean/doctl
Django 3.2
Kubectl  1.23.3

- Install git
https://git-scm.com/downloads

- gitignore
https://github.com/github/gitignore/blob/main/Python.gitignore

### Create virtual environment
* Create directory
```cmd
mkdir django-k8s 
cd django-k8s
```
* Creating Virtual environment 
```cmd
python -m venv .
```
* Activate virtual environment
```cmd
Scripts\activate.bat
```
* To see installed packages and versions
```
pip freeze
```
* Python version
```
python -V
```
* Upgrade pip
```
python.exe -m pip install --upgrade pip 
```
#### Create and Install requirements
```
copy NUL requirements.txt
```
> * Add this packages in requirements.txt
```
django>=3.2,<4.2
gunicorn
requests
django-dotenv
psycopg2-binary
django-storages
boto3
```
> * Install requeriments.txt
```
pip install -r requirements.txt
```

# 33:00
* Create virtual environment
```
python -m venv env
```
* Activate virtual environment
```
env\Scripts\activate
```
* Upgrade pip
```
python.exe -m pip install --upgrade pip 
```
* Install django
```
pip install "django>=3.2,<4.2"
```
* Create web file
```
mkdir web
```
* Create requirements.txt inside web directory
```
copy NUL web/requirements.txt
```
> * Add this to requirements.txt
```
django>=3.2,<4.2
gunicorn
requests
django-dotenv
psycopg2-binary
django-storages
boto3
```
> * Install requirements.txt
```
pip install -r web/requirements.txt
```
* Create django project inside web directory
```
django-admin startproject django_k8s web/
```
* Create gitignore
```
copy NUL .gitignore
```
> * Add this to .gitignore
```
web/.env
```
* Create .env
```
copy NUL web\.env
```
> * Add this to .env
```
DEBUG
DJANGO_SUPERUSER_USERNAME=admin
DJANGO_SUPERUSER_PASSWORD=mydjangopw
DJANGO_SUPERUSER_EMAIL=hello@teamcfe.com
DJANGO_SECRET_KEY=fix_this_later

POSTGRES_DB=dockerdc
POSTGRES_DB=mysecretpassword
POSTGRES_DB=myuser
POSTGRES_DB=postgres_db
POSTGRES_DB=5433

REDIS_HOST=redis_db
REDIS_PORT=6380
```

## 37:15
* wsgi.py
```python
import os
import pathlib
import dotenv

from django.core.wsgi import get_wsgi_application

CURRENT_DIR = pathlib.Path(__file__).resolve().parent
BASE_DIR = CURRENT_DIR.parent
ENV_FILE_PATH = BASE_DIR / ".env"

dotenv.read_dotenv(str(ENV_FILE_PATH))

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'django_k8s.settings')
```
* manage.py
```
import dotenv
```
```
if __name__ == '__main__':
    dotenv.read_dotenv()
    main()
```
>>> Test the environment variables
```
python web/manage.py shell
import os
```
>>> This prints TEXAS
```
print(os.environ.get("REGION")) 
```

* settings.py 
```
import os 
```
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

DB_USERNAME = os.environ.get("POSTGRES_USER")
DB_PASSWORD = os.environ.get("POSTGRES_PASSWORD")
DB_DATABASE = os.environ.get("POSTGRES_USER")
DB_HOST= os.environ.get("POSTGRES_HOST")
DB_PORT= os.environ.get("POSTGRES_PORT")
DB_IS_AVIAL= all([
    DB_USERNAME,
    DB_PASSWORD,
    DB_DATABASE,
    DB_HOST,
    DB_PORT
])

POSTGRES_READY=str(os.environ.get('POSTGRES_READY')) == "1"

# Password validation
```
```
DEBUG = str(os.environ.get('DEBUG')) == "1"
```

>>> To see what database is using run django
```
python web\manage.py runserver
```
>>> Here has to appear postgresql not sqlite3
```
{'default': {'ENGINE': 'django.db.backends.sqlite3', 'NAME': WindowsPath('D:/Django Professional/django-k8s/web/db.sqlite3')}}
{'default': {'ENGINE': 'django.db.backends.sqlite3', 'NAME': WindowsPath('D:/Django Professional/django-k8s/web/db.sqlite3')}}
```
* settings.py
```
SECRET_KEY = os.environ.get("DJANGO_SECRET_KEY")
```
### 47:30 Docker, Dockerfile & dockerignore
* Create .dockerignore
```
copy NUL web\.dockerignore
```
* Copy the contents of gitignore to dockerfile and modify the first lines.
> * From this
```
web/.env
.DS_Store
```
> * to this because we already has `.env` and `venv/`
``` 
.DS_Store
```
* Create dockerfile
```
copy Nul web\Dockerfile
```
* We go to dockerhub to search python image.
* Go to search python and go to tags. In my case, I am using `Python 3.11.4`
* Search image acoording to your version.
* `COPY . /app` this will copy `web` directory to `/app`
* `.dockerignore` allows to docker, ignore files like `__pycache__/`
* `WORKDIR /app` now the working directory is `/app`. Its like a `cd app` 
```
FROM python:3.11.4-slim-bullseye

COPY . /app
WORKDIR /app

RUN python3 -m venv /opt/venv

RUN pip install pip --upgrade
RUN /opt/venv/bin/pip install -r requirements.txt
RUN chmode +x entrypoint.sh

CMD ["/app/entrypoint.sh"]
```
# 55:37
* Create `entrypoint.sh`
```
copy NUL web\entrypoint.sh
```
* Add to `entrypoint.sh`. That is a `bash script` not a python script.
```
#!/bin/bash
APP_PORT=${PORT:-8000}
cd /app/
/opt/venv/bin/gunicorn --worker-tem-dir /dev/shm django_k8s.wsgi:application --bind "0.0.0.0:${APP_PORT}"
```
> * To test that.
> * Run cd to get the absolute path.
```
cd
```
> * This is the long way to run django project locally.This do the `entrypoint.sh`.
> You have to use `Scripts` directory if you are in windows.
> You have to use `bin` directory if you are in Linux.
```
"D:\Django Professional\django-k8s\env\scripts\python" web\manage.py runserver 
```  
