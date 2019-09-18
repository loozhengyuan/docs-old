# Installing LXD Hypervisor

## Installation

Install snapd package

```text
sudo apt update -y && sudo apt upgrade -y
sudo apt install snapd -y
```

Install lxd package via snap

```text
sudo snap install lxd
```

Add user to lxd group

```text
sudo usermod -a -G lxd <user>
```

## Initial Configuration

```text
lxd init
```

## Basic Administration

To create your first container, simple run the following command. Note that you can leave the container\_name optional if you like.

```text
lxc launch images:debian/stretch/amd64 <container_name>
```

To view a list of containers you have:

```text
lxc list
```

To access your container:

```text
lxc exec <container_name> -- /bin/bash
```

To stop your container:

```text
lxc stop <container_name>
```

To delete your container:

```text
lxc delete <container_name>
```

## Relevant sources:

[https://linuxcontainers.org/lxd/getting-started-cli/](https://linuxcontainers.org/lxd/getting-started-cli/)

