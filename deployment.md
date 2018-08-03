Website Deployment on Server
============================
 
There are two aspects to deployment:
 
* _Provisioning_ a new server to be able to host the code
* _Deploying_  a new version of the code to an existing server.
 
For larger, more complex sites or major changes to existing an site it may be beneficial to use a brand new server for every deployment.
 
Web Hosting Choices:
 
* Running your own (possibly virtual - Digital Ocean, Amazon, Rackspace) server
* Using a Platform-As-A-Service (PaaS) like Heroku, OpenShift or PythonAnywhere.
 
PaaS are useful for small sites.
 
### Provisioning and Deployment Procedures Summary

#### Provisioning

1. Assume we have a user account and home folder
2. add-apt-repository ppa:deadsnakes/ppa && apt update
3. apt install nginx git python3.6 python3.6-venv
4. Add Nginx config for virtual host
5. Add Systemd job for Gunicorn (including unique `SECRET_KEY`)

#### Deployment

1. Create directory in ~/sites
2. Pull down source code
3. Start virtualenv in _virtualenv_
4. `pip install -r requirements.txt`
5. `manage migrate` for database
6. `collectstatic` for static files
7. Restart Gunicorn job
8. Run FTs to check everything works

SSH on New Server
=================

On the machine you will be working on, create a new ssh key.

```shell
$ ssh-keygen
```

Login to the server as the root user and add the user with sudo rights

```shell
root@server:$ useradd -m -s /bin/bash steve
root@server:$ usermod -a -G sudo steve
passwd steve
```

Switch login to user and add the *public key* to the server.

```shell
steve@server:$ su - steve
steve@server:$ mkdir -p ~/.ssh
steve@server:$ echo SSH_PUBLIC_KEY >> ~/.ssh/authorized_keys
``` 

You should be able to now login.

Deployment on New Server
========================
 
On Ubuntu 18.04 Bionic/LTS
 
### Python 3.6
```shell
steve@server:$ sudo apt update
steve@server:$ sudo apt install python3 python3-venv
```
 
### Git
```shell
steve@server:$ sudo apt install git
```
 
### Source Code
```shell
steve@server:$ export SITENAME=url_of_website
steve@server:$ git clone repo_url ~/sites/$SITENAME
```
 
### Set up Python Virtual Environment
 
It is better to set up the virtual environment within the sites directory so that the dependencies can be easily managed as opposed to using virtualenvwrapper which will store the environment in another location.
 
We will explicitly call python by specifying the python path as opposed to using `source ./virtualenv/bin/activate`.
 
```shell
steve@server:$ python3.6 -m venv virtualenv
steve@server:$ ./virtualenv/bin/pip install -r requirements.txt
```
 
### Don't forget to Migrate the Database...
 
```shell
steve@server:$ ./virtualenv/bin/python manage.py migrate --noinput
```

### Nginx Installation

A Production level web server.

```shell
steve@server:$ sudo apt install nginx
steve@server:$ sudo systemctl start nginx
```

This should open up port 80 which is the standard port used to access urls on the internet.

### Route Nginx Requests to Website

`server: /etc/nginx/sites-available/DOMAIN`

```
server {

    listen 80;
    server_name DOMAIN;

    location /static {
        alias /home/steve/sites/DOMAIN/static;
    }

    location / {
        proxy_pass http://unix:/tmp/DOMAIN.socket;
        proxy_set_header Host $host;
    }
}
```

Use django command `python manage.py collectstatic --noinput` to collect all static files in a project into a central location for nginx to serve.

Add symlink, check that it worked and restart server

```shell
steve@server:$ sudo ln -s /etc/nginx/sites-available/$SITENAME /etc/nginx/sites-enabled/$SITENAME
steve@server:$ readlink -f $SITENAME
steve@server:$ sudo systemctl reload nginx
```

### Django Production Settings

```python
if 'DJANGO_DEBUG_FALSE' in os.environ:
    DEBUG = False
    SECRET_KEY = os.environ['DJANGO_SECRET_KEY']
    ALLOWED_HOSTS = [os.environ['SITENAME']]
else:
    DEBUG = True
    SECRET_KEY = 'insecure-key-for-dev'
    ALLOWED_HOSTS = []
```

Store these variables on the server using a `.env` file

```
DJANGO_DEBUG_FALSE=y
SITENAME=$SITENAME
DJANGO_SECRET_KEY=your_secret_key!
```

You can generate a secret key using this little command:

```shell
(
python3.6 -c"import random; print(''.join(random.SystemRandom().
choices('abcdefghijklmnopqrstuvwxyz0123456789', k=50)))"
)
```

You can then set these variables into your current terminal:

```shell
steve@server:$ set -a; source.env; set +a
```

### Start Gunicorn on Boot with Systemd

`server: /etc/systemd/system/gunicorn-[DOMAIN].service`

```
[Unit]
Description=Gunicorn server for DOMAIN

[Service]
Restart=on-failure
User=steve
WorkingDirectory=/home/steve/sites/DOMAIN
EnvironmentFile=/home/steve/sites/DOMAIN/.env

ExecStart=/home/steve/sites/DOMAIN/virtualenv/bin/gunicorn \
    --bind unix:/tmp/DOMAIN.socket \
    PROJECT.wsgi:application

[Install]
WantedBy=multi-user.target
```

Get Systemd to start Gunicorn

```shell
steve@server:$ sudo systemctl daemon-reload
steve@server:$ sudo systemctl enable gunicorn-DOMAIN
steve@server:$ sudo systemctl start gunicorn-DOMAIN
```

Automated Deployment with Fabric
================================

First install fabric3 into your environment

```shell
$ pip install fabric3
```

Create a deployment script with the main function being `deploy`. Store in `deploy_tools/fabfile.py`. Make sure you restart the gunicorn service after you deploy! Do this with the following command `sudo service gunicorn-service-name restart`.

```python
import random
from fabric.contrib.files import append, exists
from fabric.api import cd, env, local, run

REPO_URL = 'location of repo'

def deploy():
    site_folder = f'/home/{env.user}/sites/{env.host}'
    run(f'mkdir -p {site_folder}')
    with cd(site_folder):
        _get_latest_source()
        _update_virtualenv()
        _create_or_update_dotenv()
        _update_static_files()
        _update_database()
```

The fabric `run` function basically runs the requested command on the target machine. To run this script use the following command:

```shell
$ fab deploy:host=steve@mindfulspending.xyz
```

### Pulling Down Source Code with Git

```python
def _get_latest_source():
    if exists('.git'):
        run('git fetch')
    else:
        run(f'git clone {REPO_URL} .')
    current_commit = local("git log -n 1 --format=%H", capture=True)
    run(f'git reset --hard {current_commit}')
```

### Updating the Virtualenv

```python
def _update_virtualenv():
    if not exists('virtualenv/bin/pip'):
        run(f'python3.6 -m venv virtualenv')
    run('./virtualenv/bin/pip install -r requirements.txt')
```

### Creating a New .env File if Necessary

```python
def _create_or_update_dotenv():
    append('.env', 'DJANGO_DEBUG_FALSE=Y')
    append('.env', f'SITENAME={env.host}')
    current_contents = run('cat .env')
    if 'DJANGO_SECRET_KEY' not in current_contents:
        new_secret = ''.join(random.SystemRandom().choices(
            'abcdefghijklmnopqrstuvwxyz0123456789', k=50
        ))
        append('.env', f'DJANGO_SECRET_KEY={new_secret}')
```

### Updating Static Files

```python
def _update_static_files():
    run('./virtualenv/bin/python manage.py collectstatic --noinput')
```

### Migrating the Database if Necessary

```python
def _update_database():
    run('./virtualenv/bin/python manage.py migrate --noinput')
```
