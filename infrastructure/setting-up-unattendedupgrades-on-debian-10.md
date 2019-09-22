# Setting up UnattendedUpgrades on Debian 10

## Step 1: Install dependencies

Make sure you have `unattended-upgrades` and `apt-listchanges` installed on your system:

```bash
sudo apt update
sudo apt upgrade -y
```

```bash
sudo apt install unattended-upgrades apt-listchanges
```

## Step 2: Setup configuration

Run the following commands to setup `unattended-upgrades`:

```bash
echo unattended-upgrades unattended-upgrades/enable_auto_updates boolean true | sudo debconf-set-selections
```

```bash
sudo dpkg-reconfigure -f noninteractive unattended-upgrades
```

Check that auto update was correctly setup:

```bash
cat /etc/apt/apt.conf.d/20auto-upgrades
```

{% code-tabs %}
{% code-tabs-item title="/etc/apt/apt.conf.d/20auto-upgrades" %}
```text
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```
{% endcode-tabs-item %}
{% endcode-tabs %}



