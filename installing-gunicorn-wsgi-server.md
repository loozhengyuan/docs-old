# Installing Gunicorn WSGI Server

## Prerequisites

This doc assumes the following:

* Debian 9
* Python 3
* Django 2.1 \(for example sake\)

## Installation

If you are using Python virtual environment, activate your venv

```text
. venv/bin/activate
```

Install gunicorn

```text
pip3 install gunicorn
```

## Run WSGI server

Gunicorn is activated using the `gunicorn` command supplied with two main parameters. The last parameter at the back `-b 0:8001` means that Gunicorn will listen to all ip addresses `0.0.0.0` and bound to port `8001`.

If you're running this with `django`, navigate to your project root and run the following commands to make sure gunicorn is working correctly

```text
gunicorn myproject.wsgi -b 0:8001
```

## Install Gunicorn using `systemd`

Create a new systemd file by running the following command

```text
sudo nano /etc/systemd/system/myproject.service
```

Paste the following and edit myproject to whatever your project is called

```text
# /etc/systemd/system/myproject.service

[Unit]
Description=Gunicorn
After=network.target

[Service]
User=root
Group=www-data
WorkingDirectory=/path/to/myproject
ExecStart=/path/to/myproject/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind 0.0.0.0:8001 \
          --reload \
          myproject.wsgi

[Install]
WantedBy=multi-user.target
```

Optional: If you prefer a socket-based connection. Note the change in the `--bind` kwarg

```text
[Unit]
Description=Gunicorn
After=network.target

[Service]
User=root
Group=www-data
WorkingDirectory=/path/to/myproject
ExecStart=/path/to/myproject/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/home/sammy/myproject/myproject.sock \
          --reload \
          myproject.wsgi

[Install]
WantedBy=multi-user.target
```

Start the system process

```text
sudo systemctl start myproject
sudo systemctl enable myproject
```

