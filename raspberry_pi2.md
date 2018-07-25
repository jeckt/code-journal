Raspberry Pi Installation Tips & Tricks
=======================================

### Python 3.6.x Installation

#### Requirements

```shell
$ sudo apt-get update
$ sudo apt-get install build-essential tk-dev 
$ sudo apt-get install libncurses5-dev libncursesw5-dev libreadline6-dev 
$ sudo apt-get install libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev 
$ sudo apt-get install libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev
```
#### Download and Compile Python 3.6.x

```shell
$ wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tar.xz
$ tar xf Python-3.6.6.tar.xz
$ cd Python-3.6.6
$ ./configure --enable-optimizations
make
sudo make altinstall
```

*This may take a while!*

#### Clean up

```
$ sudo rm -r Python-3.6.6
$ rm Python-3.6.6.tar.xz
$ sudo apt-get --purge remove build-essential tk-dev
$ sudo apt-get --purge remove libncurses5-dev libncursesw5-dev libreadline6-dev
$ sudo apt-get --purge remove libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev
$ sudo apt-get --purge remove libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev
$ sudo apt-get autoremove
$ sudo apt-get clean
```
