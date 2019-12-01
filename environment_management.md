Environment Management with VirtualEnv
======================================

A good way to handle dependencies in an application is to use `virtualenv` and `virtualenvwrapper`. These libraries assist you in installing all your dependencies including python binaries to a "virtual" environment which can be used by your specified application

### Installation

```shell
$ sudo pip3 install virtualenv
$ sudo pip3 install virtualenvwrapper
```
### Setup

To use these tools set two of the following environment variables:

```shell
$ WORKON_HOME=/home/steve/Envs
$ VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
```

`$WORKON_HOME` defines where all the virtual environments will be installed and `$VIRTUALENVWRAPPER_PYTHON` defines the python path where the python binaries will be sourced when creating a new virtual environment.

Alternatively if you don't want to use the virtualenvwrapper then use the following shell command in the project folder

```shell
$ virtualenv venv
```

If you want to specify the python version to use in the virtual environment you could do something similar to the following

```shell
$ virtualenv --python=python3 venv
```

### Usage in Terminal

```shell
$ source /usr/local/bin/virtualenvwrapper.sh
```
or
```shell
$ source `which virtualenvwrapper.sh`
```

Alternatively if you are using virtualenv without the wrapper:
```shell
$ source /path/to/ENV/bin/activate
```

### Useful VirtualEnvWrapper Commands

Once virtualenvwrapper has been activated in the terminal the following commands can be used:

|Command               | Description                                                                       |
|----------------------|-----------------------------------------------------------------------------------|
|`$ mkvirtualenv $ENV` | Creates a new virtual environment and installs python binaries in the environment.|
|`$ workon $ENV`       | Activates the environment to work on for the terminal session.                    |
|`$ deactivate`        | Deactivates the current environment you are working on                            |


Spinning New Environments for Existing Projects 
===============================================

A requirements.txt file helps install all required dependencies when pulling a project into a new environment, whether that be for development, staging or production.

### Generate or Update Requirements file

```shell
$ pip freeze > requirements.txt
```

### Install Dependencies in New Environment

```shell
$ pip install -r requirements.txt
```
