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

### Usage in Terminal

``shell
$ source /usr/local/bin/virtualenvwrapper.sh
```
or
```shell
$ source `which virtualenvwrapper.sh`
```

### Useful VirtualEnvWrapper Commands

Once virtualenvwrapper has been activated in the terminal the following commands can be used:

|Command              | Description                                                                       |
|---------------------------------------------------------------------------------------------------------|
|`$ mkvirtualenv $ENV | Creates a new virtual environment and installs python binaries in the environment.|
|---------------------------------------------------------------------------------------------------------|
|`$ workon $ENV       | Activates the environment to work on for the terminal session.                    |
|---------------------------------------------------------------------------------------------------------|
|`$ deactivate`       | Deactivates the current environment you are working on                            |
