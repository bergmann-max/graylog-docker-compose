# Graylog-Docker-Compose
This is a set of [Docker Compose](https://docs.docker.com/compose/) files that allow you to quickly spin up a [Graylog](https://docs.graylog.org/) instance.

## Prerequisites
- [Docker](https://docs.docker.com/engine/install/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Configure Graylog

All the [Graylog configurations](https://docs.graylog.org/docs/server-conf) can be set via environment variables. Just prefix the parameter name with `GRAYLOG_` and put it in upper case.

There is an environment file (`.env.example`) where you can store these environment variables. Rename this to `.env` so Docker Compose will pick it up.

      $ cp .env.example .env


**Important:** 
To get the desired image edit the `GRAYLOG_IMAGE` environment variable in the .env file. You can look here for the available images: https://hub.docker.com/r/graylog/graylog/tags

Be sure to to set the `GRAYLOG_PASSWORD_SECRET` and `GRAYLOG_ROOT_PASSWORD_SHA2` environment variables too! Graylog won't start without these.

## Starting Graylog

After you've configured `GRAYLOG_PASSWORD_SECRET` and `GRAYLOG_ROOT_PASSWORD_SHA2`, run these commands to start the instance:

    $ docker compose up

To start it daemonized, run:

    $ docker compose up -d

It's as simple as that!

## Starting Graylog via systemd
Put the file from `systemd/graylog-docker-compose.service` to `/etc/systemd/system/`.

    $ cp systemd/graylog-docker-compose.service /etc/systemd/system/
    
Change the `WorkingDirectory` parameter with your dockerized project path. And enable the service to start automatically:

    $ systemctl enable graylog-docker-compose

## Configure Apache
To enable SSL, reverse proxy and certificate provisioning via the ACME protocol, Apache modules must be enabled:

    $ a2enmod ssl proxy_http proxy_http2 headers request md

Then you can copy the file `apache2/graylog-web.conf` to `/etc/apache2/sites-available/` edit the settings in the file and enable it:

    $ cp apache2/graylog-web.conf /etc/apache2/sites-available/
    $ nano /etc/apache2/sites-available/graylog-web.conf
    $ a2ensite graylog-web.conf


**Note:**
Of course, you can also include your SSL certificates as usual:
```apache2.conf
<VirtualHost *:443>
    ServerName graylog.example.com
    SSLEngine on
    SSLCertificateFile "/path/to/graylog.example.com.cert"
    SSLCertificateKeyFile "/path/to/graylog.example.com.key"
</VirtualHost>
```