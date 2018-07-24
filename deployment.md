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
