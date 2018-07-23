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
 
Deployment on New Server
========================
 
On Ubuntu 18.04 Bionic/LTS
 
### Python 3.6
```shell
steve@server:$ *sudo apt update*
steve@server:$ *sudo apt install python3 python3-venv*
```
 
### Git
```shell
steve@server:$ *sudo apt install git*
```
 
### Source Code
```shell
steve@server:$ *export SITENAME=url_of_website*
steve@server:$ *git clone repo_url ~/sites/$SITENAME*
```
 
### Set up Python Virtual Environment
 
It is better to set up the virtual environment within the sites directory so that the dependencies can be easily managed as opposed to using virtualenvwrapper which will store the environment in another location.
 
We will explicitly call python by specifying the python path as opposed to using `source ./virtualenv/bin/activate`.
 
```shell
steve@server:$ *python3.6 -m venv virtualenv*
steve@server:$ *./virtualenv/bin/pip install -r requirements.txt*
```
 
### Don't forget to Migrate the Database...
 
```shell
steve@server:$ *./virtualenv/bin/python manage.py migrate --noinput*
```
