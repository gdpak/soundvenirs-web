# Soundvenirs Website and Backend

[![Build Status](https://travis-ci.org/manuelkiessling/soundvenirs-backend.png?branch=master)](https://travis-ci.org/manuelkiessling/soundvenirs-backend)

## Installation

### Requirements

* Ubuntu 12.04
* git
* sqlite3
* php5-fpm
* php5-gd
* php5-uuid
* php5-sqlite
* composer
* nginx

### Setup

    cd
    sudo apt-get install git nginx sqlite3 php5-cli php5-fpm php5-gd php5-uuid php5-sqlite
    curl -sS https://getcomposer.org/installer | php
    sudo ln -s ~/composer.phar /usr/local/bin/composer

Set `listen` to `127.0.0.1:9001` in `/etc/php5/fpm/pool.d/www.conf`.

Create `/etc/nginx/sites-available/soundvenirs.com` with the following content:

    server {
        server_name www.soundvenirs.com soundvenirs.com;
        listen 80;
        access_log /var/log/nginx/soundvenirs.com.access.log;
        error_log /var/log/nginx/soundvenirs.com.error.log;
        charset utf-8;
    
        root /opt/soundvenirs.com/public;
        index index.php;
    
        location ~ \.php$ {
            fastcgi_pass 127.0.0.1:9001;
            fastcgi_index index.php;
            include fastcgi_params;
        }
    
        location / {
            if (-f $request_filename) {
                expires max;
                break;
            }
            rewrite ^(.*) /index.php last;
        }
    }

Then run

    sudo ln -s /etc/nginx/sites-available/soundvenirs.com /etc/nginx/sites-enabled/
    cd /opt
    git clone git@github.com:manuelkiessling/soundvenirs-backend.git ./soundvenirs.com
    cd /opt/soundvenirs.com
    composer install
    sudo service php5-fpm restart
    sudo service nginx restart

### Database creation

Run

    sqlite3 /var/tmp/soundvenirs.production.sqlite

Then, inside the SQLite shell, run

    CREATE TABLE sounds(
       uuid CHAR(36) PRIMARY KEY NOT NULL,
       title TEXT NOT NULL,
       lat FLOAT NULL,
       long FLOAT NULL,
       mp3url TEXT
    );
