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
gunicorn project.wsgi -b 0:8001
```

## Install Gunicorn using `systemd`

Create a new systemd file by running the following command

```text
sudo nano /etc/systemd/system/project.service
```

Paste the following and edit `project` to whatever your project is called

```text
# /etc/systemd/system/project.service

[Unit]
Description=Gunicorn
After=network.target

[Service]
User=root
Group=www-data
WorkingDirectory=/path/to/project
EnvironmentFile=/path/to/project/secret.env
ExecStart=/path/to/project/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind 0.0.0.0:8001 \
          --reload \
          project.wsgi

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
WorkingDirectory=/path/to/project
EnvironmentFile=/path/to/project/secret.env
ExecStart=/path/to/project/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/path/to/project/project/project.sock \
          --reload \
          project.wsgi

[Install]
WantedBy=multi-user.target
```

Start the system process

```text
sudo systemctl start project
sudo systemctl enable project
```

