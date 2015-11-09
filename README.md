## Requirements

* Ubuntu 15.04 / Ubuntu 15.10
* Apache 2 and mod_wsgi (for production only)
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

### Apache

```
sudo apt-get install -y apache2 libapache2-mod-wsgi-py3
sudo service apache2 restart
```

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
```

In order to set environment variables, create `jake/.env` file.
Here are available settings:

```
DATABASE_DEFAULT_PASSWORD
```

Install dependencies: `pip install -r ~/Apps/jake/requirements.txt`

Do some Django related work:

```
python ~/Apps/jake/manage.py migrate
python ~/Apps/jake/manage.py createsuperuser
```

Create new virtual host in `/etc/apache2/sites-available/jake.conf`:

```
<VirtualHost *:80>
    ServerName jake.karciauskas.lt
    ServerAlias www.jake.karciauskas.lt

    # Insert the full path to the wsgi.py-file here.
    WSGIScriptAlias / /home/gytis/Apps/jake/jake/wsgi.py

    # PROCESS_NAME specifies a distinct name of this process.
    #   see: https://code.google.com/p/modwsgi/wiki/ConfigurationDirectives#WSGIDaemonProcess
    # PATH/TO/PROJECT_ROOT is the full path to your project's root directory,
    #   containing your project files.
    # PATH/TO/VIRTUALENV/ROOT: If you are using a virtualenv specify the full
    #   path to its directory.
    #   Generally you must specify the path to Python's site-packages.
    WSGIDaemonProcess jake python-path=/home/gytis/Apps/jake:/home/gytis/VirtualEnvs/jake/lib/python3.4/site-packages/

    # PROCESS_GROUP specifies a distinct name for the process group
    #   see: https://code.google.com/p/modwsgi/wiki/ConfigurationDirectives#WSGIProcessGroup
    WSGIProcessGroup jake

    <Directory /home/gytis/Apps/jake/jake>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>
</VirtualHost>
```

Then enable site `a2ensite jake.conf` and restart Apache server
`service apache2 reload`.
