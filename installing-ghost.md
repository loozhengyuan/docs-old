# Installing Ghost

Ghost is a beautiful blogging platform that uses the Markdown language. It also comes with a WYSIWYG editor that makes editing and creating new posts a visual breeze. Though Ghost is not officially supported on Debian, I managed to get it installed \(albeit with some pain\), so it is important that I get this post up quick so I can recall how I got it working :\)

## Step 1: Preparing for installation

Ensure that the system is updated

```text
sudo apt update -y && sudo apt upgrade -y
```

Install dependencies

```text
sudo apt install nginx mysql-server curl -y
```

Installing npm

```text
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash 
sudo apt install -y nodejs
```

Installing ghost's CLI environment

```text
sudo npm i -g ghost-cli
```

Creating installation directory

```text
sudo mkdir -p /var/www/ghost
sudo chown [user]:[user] /var/www/ghost
```

## Step 2: Crucial preparation steps

When I tried to install my version of Ghost, I kept encountering this error `ER_NOT_SUPPORTED_AUTH_MODE`. Apparently, I was supposed to be prompted to enter my root password while installing `mysql-server` but that did not happen. So, right now, we'll need to set a root password.

This connects to mysql

```text
sudo mysql -u root
```

In the mysql prompt, run:

```text
use mysql;
update user set authentication_string=password(''), plugin='mysql_native_password' where user='root';
```

Exit mysql

```text
\q
```

While I had varied successes with this, you may consider to restart to make sure everything is in order before we begin installation.

```text
sudo reboot
```

## Step 3: Installation process

Change to the installation directory

```text
cd /var/www/ghost
```

You may have to run this without 'sudo' because they dont allow you to run as 'root'

```text
sudo ghost install
```

You will be prompted for the following pieces of information:

**Enter your blog url: \[**[http://localhost:2368](http://localhost:2368)**\]** This is the url your blog will be available at and must include the protocol. e.g [http://myblog.com](http://myblog.com) for HTTP or [https://myblog.com](https://myblog.com) for HTTPS.

If you want to setup SSL, your domain must already be pointing at your server. If your domain is ready and resolved, then you can enter the https version of your domain, and choose "yes" later when asked if you want to setup SSL.

If your domain is not yet ready, enter the http version of your domain and choose no when prompted about SSL. When your domain is resolved, you can run `ghost config url [https domain]` && ghost setup ssl to run the ssl setup.

**Enter your MySQL hostname \[localhost\]:** This determines where your MySQL database can be accessed from. For the most cases the user installs MySQL on the same server, in this case use localhost \(press \[enter\] to use the default value\).

**Enter your MySQL username:** Enter your MySQL username. If you have already created a mysql user, password and database with the correct credentials ready for Ghost, enter those details now. Else, enter root and your MySQL root password and Ghost will generate a custom MySQL user for you.

**Enter your MySQL password: \[hidden\]** The password for the MySQL user you entered in the previous step.

**Ghost database name:** Here you have to enter the name of your database. If you install multiple instances of ghost on your server you need to specify a different database for each instance. If the database you entered does not exist already and you provided your root credentials it will be created for you.

If you pass a non-root MySQL username and password, this database must already exist and your user must have privileges for this database.

**Do you wish to set up a ghost MySQL user?** If you provided your root MySQL user, Ghost CLI can create a custom MySQL user that can only access/edit your Ghost database. This is recommended, and Ghost-CLI takes care of this for you if you accept.

**Do you wish to set up nginx?** Sets NGINX up for your blog enabling it to be viewed by the outside world. You can optionally set this up yourself.

**Do you wish to set up ssl?** If you do not already have a valid ssl certificate installed for your blog and wish to use secure protocol, Ghost-CLI can take of this for you using the Let's Encrypt certification service. Otherwise you have to setup ssl by your own.

Your domain must have resolved to your server in order for the SSL setup to work. If you choose no now, you can run ghost setup ssl later to rerun this step.

**Enter your email \(used for SSL certificate generation\)** This is required for SSL certification so that you can be kept informed if there is any issue with your certificate such as requiring renewal.

**Do you wish to set up systemd?** systemd is the recommended process manager tool for keeping Ghost running. Choose yes to have it configured for you, or no if you're happy setting up process management yourself.

**Do you want to start Ghost?** Choose whether you want to have Ghost running right away.

## Step 4: Accessing your blog

If you're like me, you'll be wondering why you are unable to access the blog you just created. By default, ghost only listens on your loopback interface `127.0.0.1`. Hence, we would need to change it so that we can access it publicly.

Stop all processes

```text
sudo ghost stop
```

Reconfigures listening ip; you'll need to answer all the questions again

```text
sudo ghost config --ip '0.0.0.0'
```

Restart ghost

```text
sudo ghost start
```

Now, try to access via the URL you've specified in the config. If you're not sure if ghost is running correctly or not, try:

```text
sudo ghost status
```

## Important resources

Official Installation Guide - [https://docs.ghost.org/v1/docs/install](https://docs.ghost.org/v1/docs/install) Ghost CLI Guide - [https://docs.ghost.org/v1/docs/ghost-cli](https://docs.ghost.org/v1/docs/ghost-cli) Troubleshooting Guide - [https://docs.ghost.org/v1/docs/troubleshooting](https://docs.ghost.org/v1/docs/troubleshooting)

