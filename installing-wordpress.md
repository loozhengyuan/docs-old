# Installing Wordpress

## Prerequisites

This doc assumes the following:
- Debian 9

## Installation

Ensure that the system is updated
```shell
sudo apt update -y && sudo apt upgrade -y
```

Install MariaDB and dependencies
```shell
sudo apt install mariadb-server
```

Install PHP and dependencies
```shell
sudo apt install php-fpm php-mysql
sudo apt install php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip
```

Install nginx and dependencies
```shell
sudo apt install nginx
```

## Setup MariaDB

Run the initial setup utility
```shell
sudo mysql_secure_installation
```

Quoted from DigitalOcean's [article](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mariadb-php-lamp-stack-debian9):

This will take you through a series of prompts where you can make some changes to your MariaDB installation’s security options. The first prompt will ask you to enter the current database root password. This is an administrative account in MariaDB that has increased privileges. Think of it as being similar to the root account for the server itself (although the one you are configuring now is a MariaDB-specific account). Because you just installed MariaDB and haven’t made any configuration changes yet, this password will be blank, so just press `ENTER` at the prompt.

The next prompt asks you whether you'd like to set up a database root password. Type `N` and then press `ENTER`. In Debian, the root account for MariaDB is tied closely to automated system maintenance, so we should not change the configured authentication methods for that account. Doing so would make it possible for a package update to break the database system by removing access to the administrative account. Later, we will cover how to optionally set up an additional administrative account for password access if socket authentication is not appropriate for your use case.

From there, you can press `Y` and then `ENTER` to accept the defaults for all the subsequent questions. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MariaDB immediately respects the changes you have made.

In new installs on Debian systems, the root MariaDB user is set to authenticate using the unix_socket plugin by default rather than with a password. This allows for some greater security and usability in many cases, but it can also complicate things when you need to allow an external program (e.g., phpMyAdmin) administrative rights.

Enter with MariaDB client
```shell
sudo mariadb
```

```shell
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```

```shell
GRANT ALL ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
```

```shell
FLUSH PRIVILEGES;
```

```shell
EXIT;
```

## Setup Nginx

Enable firewall
```shell
sudo ufw allow http
sudo ufw allow https
sudo ufw allow ssh
sudo ufw enable
```

Create config file for site
```shell
sudo nano /etc/nginx/sites-available/your_domain
```

Configuration if mounted on root
```shell
server {

    root /var/www/www.example.com;
    index index.php index.html index.htm;

    server_name www.example.com;

    location / {
        try_files $uri $uri/ /index.php?q=$uri&$args;   
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
    }

}
server {
    
    listen 80;
    listen [::]:80;

    server_name example.com www.example.com;

    return 301 https://www.example.com$request_uri;

}
server {

    listen 443 ssl;
    listen [::]:443 ssl;
    
    server_name example.com;

    return 301 https://www.example.com$request_uri;

}
```

Configuration if mounted on subfolder
```shell
server {

    root /var/www/www.example.com;
    index index.php index.html index.htm;

    server_name www.example.com;

    location = /favicon.ico { access_log off; log_not_found off; }

    location /static {
        root /var/www/www.example.com;
    }

    location /wordpress {
        root /var/www/www.example.com;
        try_files $uri $uri/ /wordpress/index.php?q=$uri&$args;   
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/path/to/project/project.sock;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
    }

}
server {
    
    listen 80;
    listen [::]:80;

    server_name example.com www.example.com;

    return 301 https://www.example.com$request_uri;

}
server {

    listen 443 ssl;
    listen [::]:443 ssl;
    
    server_name example.com;

    return 301 https://www.example.com$request_uri;

}
```

Check configuration
```shell
sudo nginx -t
```

Reload configuration
```shell
sudo systemctl daemon-reload
sudo systemctl reload nginx
```

## Download and install Wordpress

Download wordpress
```shell
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
```

Extract compressed files
```shell
tar xzvf latest.tar.gz
```

Copy the sample config
```shell
cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php
```

Create `upgrade` directory
```shell
mkdir /tmp/wordpress/wp-content/upgrade
```

Copy to `/var/www/` directory
```shell
sudo cp -a /tmp/wordpress/. /var/www/wordpress
```

Update ownership and permissions
```shell
sudo chown -R www-data:www-data /var/www/wordpress
sudo find /var/www/wordpress/ -type d -exec chmod 750 {} \;
sudo find /var/www/wordpress/ -type f -exec chmod 640 {} \;
```

Get secure keys
```shell
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```

Edit config file
```shell
sudo nano /var/www/wordpress/wp-config.php
```

```shell
. . .

define('DB_NAME', 'wordpress');

/** MySQL database username */
define('DB_USER', 'wordpressuser');

/** MySQL database password */
define('DB_PASSWORD', 'password');

. . .

define('AUTH_KEY',         '1jl/vqfs<XhdXoAPz9 DO NOT COPY THESE VALUES c_j{iwqD^<+c9.k<J@4H');
define('SECURE_AUTH_KEY',  'E2N-h2]Dcvp+aS/p7X DO NOT COPY THESE VALUES {Ka(f;rv?Pxf})CgLi-3');
define('LOGGED_IN_KEY',    'W(50,{W^,OPB%PB<JF DO NOT COPY THESE VALUES 2;y&,2m%3]R6DUth[;88');
define('NONCE_KEY',        'll,4UC)7ua+8<!4VM+ DO NOT COPY THESE VALUES #`DXF+[$atzM7 o^-C7g');
define('AUTH_SALT',        'koMrurzOA+|L_lG}kf DO NOT COPY THESE VALUES  07VC*Lj*lD&?3w!BT#-');
define('SECURE_AUTH_SALT', 'p32*p,]z%LZ+pAu:VY DO NOT COPY THESE VALUES C-?y+K0DK_+F|0h{!_xY');
define('LOGGED_IN_SALT',   'i^/G2W7!-1H2OQ+t$3 DO NOT COPY THESE VALUES t6**bRVFSD[Hi])-qS`|');
define('NONCE_SALT',       'Q6]U:K?j4L%Z]}h^q7 DO NOT COPY THESE VALUES 1% ^qUswWgn+6&xqHN&%');

. . .

define('FS_METHOD', 'direct');
```

## [Optional] Installing Caddy instead of Nginx

Refer to other document on setting up Caddy Web Server

Caddyfile:
```shell
example.com {
    root /var/www/wordpress
    gzip
    fastcgi / /run/php/php7.0-fpm.sock php
    rewrite {
        if {path} not_match ^\/wp-admin
        to {path} {path}/ /index.php?_url={uri}
    }
}
```

## Complete setup

Navigate to the following URL and complete setup
```shell
https://server_domain_or_IP
```

## Relevant Guides

- [https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-debian-9](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-debian-9)
- [https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-debian-9](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-debian-9)
- [https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-debian-9](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-debian-9)
- [https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lemp-on-debian-9](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lemp-on-debian-9)