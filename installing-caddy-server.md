# Installing Caddy Server

## Installation

Installs `curl` if you haven't already have it installed
```shell
sudo apt install curl
```

One-click install script by Caddy
```shell
curl https://getcaddy.com | bash -s personal hook.service
```

## Configuration

Create and edit Caddyfile
```shell
cd ~
```
```shell
sudo touch Caddyfile
```
```shell
sudo nano Caddyfile
```

Copy and paste these settings
```shell
# ~/Caddyfile

sub.example.tld {
    proxy / localhost:8000 {
        transparent
    }
}

sub.example.tld/static {
    root /var/www/sub.example.tld/static
}

```

Test to make sure it is working
```shell
cd ~
```
```shell
sudo caddy
```

## Convert to run as a service

Installs caddy as a service
```shell
caddy -service install -agree -email user@example.com -conf /path/to/Caddyfile
```

Check that caddy is running
```shell
sudo caddy -service start
```
```shell
sudo caddy -service status
```

## Increasing file descriptor limit

Increase file descriptor limit from 1024 to 8192 by editing the `/etc/security/limits.conf` file
```shell
sudo nano /etc/security/limits.conf
```

Add these two lines in the file:
```shell
# /etc/security/limits.conf

root               soft    nofile          8192
root               hard    nofile          8192
```

_Tip: If you do not have `root` access, you should change `root` to `*` instead._