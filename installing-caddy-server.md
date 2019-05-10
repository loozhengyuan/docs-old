# Installing Caddy Server

## Installation

Installs `curl` if you haven't already have it installed

```text
sudo apt install curl
```

One-click install script by Caddy

```text
curl https://getcaddy.com | bash -s personal hook.service
```

## Configuration

Create and edit Caddyfile

```text
cd ~
```

```text
sudo touch Caddyfile
```

```text
sudo nano Caddyfile
```

Copy and paste these settings

```text
# ~/Caddyfile

sub.example.tld {
    proxy / localhost:8000 {
        transparent
    }
}
```

_Optional: If you want to bind to a unix socket, you can do so with the following:_

```text
# ~/Caddyfile

sub.example.tld {
    proxy / unix:/path/to/unix.sock {
        transparent
    }
}
```

_Optional: If you want to redirect non-www to www, you can do so like this:_

```text
example.tld {
    redir https://www.example.tld{uri}
}
```

_Optional: If you want serve locally-hosted static files, you can also do so like this:_

```text
sub.example.tld/static {
    root /var/www/sub.example.tld/static
}
```

Test to make sure it is working

```text
cd ~
```

```text
sudo caddy
```

## Convert to run as a service

Installs caddy as a service

```text
caddy -service install -agree -email user@example.com -conf /path/to/Caddyfile
```

Check that caddy is running

```text
sudo caddy -service start
```

```text
sudo caddy -service status
```

## Increasing file descriptor limit

Increase file descriptor limit from 1024 to 8192 by editing the `/etc/security/limits.conf` file

```text
sudo nano /etc/security/limits.conf
```

Add these two lines in the file:

```text
# /etc/security/limits.conf

root               soft    nofile          8192
root               hard    nofile          8192
```

_Tip: If you do not have_ `root` _access, you should change_ `root` _to_ `*` _instead._

