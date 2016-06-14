# Installing Graphite-Web with ATSD Storage Finder

Using this guide you can install Graphite-Web including the, Carbon daemons, Whisper database and ATSD Storage Finders.

Install dependencies:

```
sudo apt-get install\
 python-cairo-dev\
 python-dev\
 fontconfig\
 python-fontconfig\
 apache2\
 libapache2-mod-wsgi\
 memcached\
 python-ldap\
 rabbitmq-server\
 rrdtool\
 python-rrdtool\
 apache2-utils\
 libapache2-mod-python\
 sqlite3\
 python-pysqlite2\
 python-pip
```

```
sudo pip install\
 django\
 django-tagging\
 twisted\
 zope.interface\
 pytz\
 python-memcached\
 txAMQP\
 pyparsing
```

Install Graphite Carbon and Whisper:

```
sudo pip install https://github.com/graphite-project/carbon/archive/master.zip
```

```
sudo pip install https://github.com/graphite-project/whisper/archive/master.zip
```

Install ATSD Storage Finder:

```
sudo pip install https://github.com/axibase/atsd-graphite-finder/archive/master.zip
```

Install Graphite-Web with atsd_finder:

```
sudo pip install https://github.com/axibase/graphite-web/archive/atsd_storage.zip
```

Configure Graphite:

You can find example files in `/opt/graphite/conf` directory.

```
cd /opt/graphite/conf
sudo cp carbon.conf.example carbon.conf
sudo cp storage-schemas.conf.example storage-schemas.conf
sudo cp graphite.wsgi.example /opt/graphite/conf/graphite.wsgi
```

Create `local_settings.py`:

```
cd /opt/graphite/webapp/graphite
sudo cp local_settings.py.example local_settings.py
```

In `local_settings.py` change the `SECRET_KEY` setting to a random number.

Configure your finders:

In the `local_settings.py` file specify the storage finders, ATSD hostname/IP, ATSD port, ATSD username and ATSD password ([as described here](https://axibase.com/products/axibase-time-series-database/writing-data/graphite-format/storage-finder/)):

```
STORAGE_FINDERS = (
    'atsd_finder.AtsdFinder',
    'atsd_finder.AtsdFinderV',
    'graphite.finders.standard.StandardFinder',
)
```

```
ATSD_CONF = {
    'url': 'http://atsd_server:8088',
    'username': 'atsd_username',
    'password': 'secret_pwd',
}
```

Configure Apache2 wsgi:

```
cd /opt/graphite/examples
sudo cp example-graphite-vhost.conf /etc/apache2/sites-available/graphite-web.conf
```

Disable default site configuration:

```
sudo a2dissite 000-default
```

Enable graphite-web site configuration:

```
sudo a2ensite graphite-web
```

Setup django database schema:

```
sudo PYTHONPATH=/opt/graphite/webapp django-admin.py syncdb --settings=graphite.settings
```

Set Apache2 ownership for the storage directory:

```
sudo chown -R www-data:www-data /opt/graphite/storage/
```

Restart Apache2:

```
sudo /etc/init.d/apache2 reload
```

Web interface should now be available on local port 80, ATSD metrics should also be visible.

Run carbon-cache:

```
cd /opt/graphite/bin
sudo python carbon-cache.py start
```

Within a few minutes you should see carbon metrics in the web interface.

