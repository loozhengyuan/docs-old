# Installing PostgreSQL

## Step 1: Prepping and installing Postgresql

Ensure that the system is updated

```text
sudo apt update
sudo apt upgrade
```

Install required packages

```text
sudo apt install postgresql postgresql-contrib
```

Check if its installed correctly

```text
ps -ef | grep postgre
```

## Step 2: Basic configuration

The first step is to allow remote connections to access the database. Depending on your use, this step may not be needed. If you are unable to find the relevant directory, run `sudo find / -name postgresql.conf` to locate the files on your system.

```text
sudo nano /etc/postgresql/9.6/main/postgresql.conf
```

Next, uncomment and change the listening address from `localhost` to `*`:

```text
listen_addresses = '*'
```

The second step would be to adjust the authentication methods and allowed hosts. Navigate to the file:

```text
sudo nano /etc/postgresql/9.6/main/pg_hba.conf
```

Next, replace these entries for `ipv4` and `ipv6` section:

```text
host    all             all              0.0.0.0/0                       md5
host    all             all              ::/0                            md5
```

Change the default auth method for local connections to `md5` to make the authentication less confusing. \(Leave the entry for `postgres` alone\)

```text
local    all             all                                          md5
```

Lastly, restart the server to allow changes to take effect.

```text
sudo service postgresql restart
```

## Step 2: Setting up users and database

Connect to default admin account to create roles

```text
sudo -u postgres createuser --interactive --pwprompt
sudo -u postgres createdb -O NewUser NewUser
```

To connect via that specific user \(OPTIONAL\)

```text
sudo -u newUser psql
```

Enter into postgresql shell \(press \q to exit\)

```text
psql -d newpgdatabase -U newpguser #for specific user/db
```

\(Optional\) Adding new user to UNIX database \(ONLY FOR IDENT/PEER AUTH\)

```text
sudo adduser newUser
```

## Other useful commands:

Here are a few commands that can help you get an idea of your current environment:

```text
\?: Get a full list of psql commands, including those not listed here.
\h: Get help on SQL commands. You can follow this with a specific command to get help with the syntax.
\q: Quit the psql program and exit to the Linux prompt.
\d: List available tables, views, and sequences in current database.
\du: List available roles
\dp: List access privileges
\dt: List tables
\l: List databases
\c: Connect to a different database. Follow this by the database name.
\password: Change the password for the username that follows.
\conninfo: Get information about the current database and connection.
```

With these commands you should be able to navigate the PostgreSQL databases, tables, and roles in no time.

