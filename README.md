## Requirements

* Ubuntu 15.04 / Ubuntu 15.10
* Python 3.4
* PostgreSQL 9.4
* Pip
* VirtualEnv
* Git

## Locales

Sometimes VPS has no configured locales, you can test it by executing
`perl -e exit`. If it outputs some warnings, you should generate locales
by doing this:

```
echo -e 'LANG=en_US.UTF-8\nLC_ALL=en_US.UTF-8' > /etc/default/locale
locale-gen en_US.UTF-8
```

Logout, login, and then command `perl -e exit` should output nothing.

## Installation

### PostgreSQL

`sudo apt-get install -y postgresql-9.4 python-psycopg2 libpq-dev`

### Python

`sudo apt-get install -y python3 python3-dev`

### Pip

`sudo apt-get install -y python-pip`

### VirtualEnv

`sudo apt-get install -y python-virtualenv`

### Git

`sudo apt-get install -y git`

## Preparation

Create database and user:

```
su
su - postgres
createuser jake -P
createdb -O jake jakedb
logout
exit
```

or:

```
sudo -i -u postgres
createuser jake -P
createdb -O jake jakedb
logout
```

Set up an environment:

```
virtualenv -p /usr/bin/python3.4 ~/VirtualEnvs/jake
git clone https://github.com/gyKa/jake.git ~/Apps/jake
source ~/VirtualEnvs/jake/bin/activate
pip install -r ~/Apps/jake/requirements.txt
```
