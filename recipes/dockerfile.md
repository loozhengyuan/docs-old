# Dockerfile

## Django

```text
# Use python3 image
FROM python:3-slim

# Add maintainer label
LABEL maintainer="Your email"

# Set working directory
WORKDIR /app

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# Install dependencies
RUN pip install --upgrade pip setuptools wheel
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# Copy files
COPY project/ project/
COPY app/ app/
COPY manage.py manage.py

# Create unprivileged system user
# The -l flag is used as a workaround due to an unresolved bug:
# https://github.com/golang/go/issues/13548
RUN groupadd -r docker && useradd -l -r -s /bin/false -g docker docker
RUN chown -R docker:docker /app
USER docker

# Set entrypoint
# ENTRYPOINT ["gunicorn", "--access-logfile", "-", "--workers", "3", "--bind", "unix:project/project.sock", "--reload", "project.wsgi"]
ENTRYPOINT ["gunicorn", "--access-logfile", "-", "--workers", "3", "--bind", "0.0.0.0:8000", "--reload", "project.wsgi"]

```

